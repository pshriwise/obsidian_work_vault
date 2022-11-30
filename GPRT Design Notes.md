#ray-tracing 

### gprt.cpp

First comes a lot of error handling and debugging macros. If we were using C++, they could be moved to `const expr` functions. We could also wrap them in `extern "C"` functions like we do in OpenMC, but that's for a later time. 

Next is the `gprtDeviceCode` mpa. This map contains a set of program handles as keys and the bytes of the compiled program as a byte vector. This may contain the following keys:

 - 

```C++
// Contains definitions for internal entry points
// (bounds programs, transform programs...)
extern std::map<std::string, std::vector<uint8_t>> gprtDeviceCode;
```

Then there are forward declarations for different structs used by GPRT. I'll detail their member variables and implementation later.

```c++
// forward declarations...
struct Geom;
struct GeomType;
struct TriangleGeom;
struct TriangleGeomType;
struct AABBGeom;
struct AABBGeomType;
```

Next is the forward declaration of a bunch of Vulkan SDK functions in the `gprt` namespace. It's smart to do this inside a namespace so they can't be accidentally used elsewhere accidentally.
```C++
namespace gprt{
PFN_vkGetBufferDeviceAddressKHR vkGetBufferDeviceAddress;
PFN_vkCreateAccelerationStructureKHR vkCreateAccelerationStructure;
PFN_vkDestroyAccelerationStructureKHR vkDestroyAccelerationStructure;
PFN_vkGetAccelerationStructureBuildSizesKHR vkGetAccelerationStructureBuildSizes;
PFN_vkGetAccelerationStructureDeviceAddressKHR vkGetAccelerationStructureDeviceAddress;
PFN_vkCmdBuildAccelerationStructuresKHR vkCmdBuildAccelerationStructures;
PFN_vkBuildAccelerationStructuresKHR vkBuildAccelerationStructures;
PFN_vkCmdTraceRaysKHR vkCmdTraceRays;
PFN_vkGetRayTracingShaderGroupHandlesKHR vkGetRayTracingShaderGroupHandles;
PFN_vkCreateRayTracingPipelinesKHR vkCreateRayTracingPipelines;

PFN_vkCreateDebugUtilsMessengerEXT vkCreateDebugUtilsMessengerEXT;
PFN_vkDestroyDebugUtilsMessengerEXT vkDestroyDebugUtilsMessengerEXT;
VkDebugUtilsMessengerEXT debugUtilsMessenger;

PFN_vkCreateDebugReportCallbackEXT vkCreateDebugReportCallbackEXT;
PFN_vkDestroyDebugReportCallbackEXT vkDestroyDebugReportCallbackEXT;
VkDebugReportCallbackEXT debugReportCallback;
}
```

#### Struct: Stage

``` C++
// for copying transforms into the instance buffer
struct Stage {
// std::string fillInstanceDataEntryPoint = "gprtFillInstanceData";
// VkPipelineLayout fillInstanceDataPipelineLayout;
// VkShaderModule fillInstanceDataShaderModule;
// VkPipeline fillInstanceDataPipeline;

std::string entryPoint;
VkPipelineLayout layout;
VkShaderModule module;
VkPipeline pipeline;
};
```

### Struct: Module

```C++
struct Module {

GPRTProgram program;

Module(GPRTProgram program) {
this->program = program;
}
  
std::vector<uint32_t> getBinary(std::string entryType) {
// program is null terminated, skip last byte
size_t sizeOfProgram = program[entryType].size() - 1; 
std::vector<uint32_t> finalProgram(sizeOfProgram / 4);
memcpy(finalProgram.data(), program[entryType].data(), sizeOfProgram);
return finalProgram;
}

};
```

### Struct: Buffer

```c++
struct Buffer {

VkDevice device;
VkPhysicalDeviceMemoryProperties memoryProperties;
VkCommandBuffer commandBuffer;
VkQueue queue;

/** @brief Usage flags to be filled by external source at buffer creation */

VkBufferUsageFlags usageFlags;

/** @brief Memory property flags to be filled by external source at buffer creation */
VkMemoryPropertyFlags memoryPropertyFlags;

bool hostVisible;

VkBuffer buffer = VK_NULL_HANDLE;

VkDeviceMemory memory = VK_NULL_HANDLE;

VkDeviceAddress address = 0;

  

struct StagingBuffer {

VkBuffer buffer = VK_NULL_HANDLE;

VkDeviceMemory memory = VK_NULL_HANDLE;

VkDeviceAddress address = 0;

} stagingBuffer;

  

VkDeviceSize size = 0;

VkDeviceSize alignment = 0;

void* mapped = nullptr;

  

VkResult map(VkDeviceSize mapSize = VK_WHOLE_SIZE, VkDeviceSize offset = 0)

{

if (hostVisible) {

if (mapped) return VK_SUCCESS;

else return vkMapMemory(device, memory, offset, size, 0, &mapped);

}

else {

VkResult err;

VkCommandBufferBeginInfo cmdBufInfo{};

cmdBufInfo.sType = VK_STRUCTURE_TYPE_COMMAND_BUFFER_BEGIN_INFO;

err = vkBeginCommandBuffer(commandBuffer, &cmdBufInfo);

if (err) GPRT_RAISE("failed to begin command buffer for buffer map! : \n" + errorString(err));

  

// To do, consider allowing users to specify offsets here...

VkBufferCopy region;

region.srcOffset = 0;

region.dstOffset = 0;

region.size = size;

vkCmdCopyBuffer(commandBuffer, buffer, stagingBuffer.buffer, 1, &region);

  

err = vkEndCommandBuffer(commandBuffer);

if (err) GPRT_RAISE("failed to end command buffer for buffer map! : \n" + errorString(err));

  

VkSubmitInfo submitInfo;

submitInfo.sType = VK_STRUCTURE_TYPE_SUBMIT_INFO;

submitInfo.pNext = NULL;

submitInfo.waitSemaphoreCount = 0;

submitInfo.pWaitSemaphores = nullptr;//&acquireImageSemaphoreHandleList[currentFrame];

submitInfo.pWaitDstStageMask = nullptr;//&pipelineStageFlags;

submitInfo.commandBufferCount = 1;

submitInfo.pCommandBuffers = &commandBuffer;

submitInfo.signalSemaphoreCount = 0;

submitInfo.pSignalSemaphores = nullptr;//&writeImageSemaphoreHandleList[currentImageIndex]};

  

err = vkQueueSubmit(queue, 1, &submitInfo, nullptr);

if (err) GPRT_RAISE("failed to submit to queue for buffer map! : \n" + errorString(err));

  

err = vkQueueWaitIdle(queue);

if (err) GPRT_RAISE("failed to wait for queue idle for buffer map! : \n" + errorString(err));

  

// todo, transfer device data to host

if (mapped) return VK_SUCCESS;

else return vkMapMemory(device, stagingBuffer.memory, offset, mapSize, 0, &mapped);

}

}

  

void unmap()

{

if (hostVisible) {

if (mapped) {

vkUnmapMemory(device, memory);

mapped = nullptr;

}

}

else {

VkResult err;

VkCommandBufferBeginInfo cmdBufInfo{};

cmdBufInfo.sType = VK_STRUCTURE_TYPE_COMMAND_BUFFER_BEGIN_INFO;

err = vkBeginCommandBuffer(commandBuffer, &cmdBufInfo);

if (err) GPRT_RAISE("failed to begin command buffer for buffer map! : \n" + errorString(err));

  

// To do, consider allowing users to specify offsets here...

VkBufferCopy region;

region.srcOffset = 0;

region.dstOffset = 0;

region.size = size;

vkCmdCopyBuffer(commandBuffer, stagingBuffer.buffer, buffer, 1, &region);

  

err = vkEndCommandBuffer(commandBuffer);

if (err) GPRT_RAISE("failed to end command buffer for buffer map! : \n" + errorString(err));

VkSubmitInfo submitInfo;

submitInfo.sType = VK_STRUCTURE_TYPE_SUBMIT_INFO;

submitInfo.pNext = NULL;

submitInfo.waitSemaphoreCount = 0;

submitInfo.pWaitSemaphores = nullptr;//&acquireImageSemaphoreHandleList[currentFrame];

submitInfo.pWaitDstStageMask = nullptr;//&pipelineStageFlags;

submitInfo.commandBufferCount = 1;

submitInfo.pCommandBuffers = &commandBuffer;

submitInfo.signalSemaphoreCount = 0;

submitInfo.pSignalSemaphores = nullptr;//&writeImageSemaphoreHandleList[currentImageIndex]};

  

err = vkQueueSubmit(queue, 1, &submitInfo, nullptr);

if (err) GPRT_RAISE("failed to submit to queue for buffer map! : \n" + errorString(err));

  

err = vkQueueWaitIdle(queue);

if (err) GPRT_RAISE("failed to wait for queue idle for buffer map! : \n" + errorString(err));

// todo, transfer device data to device

if (mapped) {

vkUnmapMemory(device, stagingBuffer.memory);

mapped = nullptr;

}

}

}
```