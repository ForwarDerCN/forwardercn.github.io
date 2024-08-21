# Tips for CUDA Programming

## Common CUDA

## With `<thrust/sort>` library

While we want to sort a struct array by one key, we can use `thrust::sort` with a custom comparator. Here is an example:

```cpp
#include <thrust/sort.h>
#include <thrust/device_vector.h>
#include <thrust/execution_policy.h>

struct KeyStruct 
{
    int key;
    float value[64];   // it intends to be a large array
};

void sortStructArray(KeyStruct* d_array, int size) 
{
    thrust::device_ptr<KeyStruct> d_ptr(d_array);
    thrust::sort(
        thrust::device, 
        d_ptr, 
        d_ptr + size, 
        [] __device__ (const KeyStruct& a, const KeyStruct& b) {
            return a.key < b.key;
        });             // using a custom comparator: lambda function
}
```

We find it extremely slow when the struct array is large. The possible reason is that thrust swap the whole struct when sorting, which brings great **cache miss**.


One way to solve this problem is to use a `key-value` pair array. We can sort the `key` array and then rearrange the `value` array. Here is an example:

```cpp
#include <thrust/sort.h>
#include <thrust/device_vector.h>
#include <thrust/execution_policy.h>

struct KeyStruct 
{
    int key;
    float value[64];   // it intends to be a large array
};

```

