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

Next is the forward declaration of a bunch of Vulkan SDK functions in the `gprt` namespace. It's smart to 
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