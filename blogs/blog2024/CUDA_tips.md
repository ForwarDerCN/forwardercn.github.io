# Tips for CUDA Programming

Here are some tips for CUDA programming. The original thinking of using CUDA is to speed up the program by using the GPU. So we concentrate on the performance of the program.

## Common CUDA

### Memory Management

We always meet the scene that the program loops once and once, using the same memory space. In this case, we can use `cudaMalloc` to allocate memory space **only once** and use `cudaMemcpy` to copy data between the host and the device. 

The important thing is that we only allocate memory space once and **use it repeatedly**. DON'T allocate memory space at the beginning of each loop and free it at the end of each loop. It is a huge waste of time.

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
        }           // using a custom comparator: lambda function
    );             
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

__global__ extractKeyArray(int* d_key, KeyStruct* d_array, int size)
{
    int idx = threadIdx.x + blockIdx.x * blockDim.x;
    if (idx < size)
    {
        d_key[idx] = d_array[idx].key;
    }
}

__global__ rearrangeValueArray(float* d_value, KeyStruct* d_array, int* d_seq, int size)
{
    int idx = threadIdx.x + blockIdx.x * blockDim.x;
    if (idx < size)
    {
        d_value[idx] = d_array[d_seq[idx]].value;
    }
}

void sortStructArray(KeyStruct* d_array, int size) 
{   
    int block_size = 256;
    int grid_size = (size + block_size - 1) / block_size;

    // Step1: extract the key array to a new device array
    thrust::device_vector<int> d_key(size);
    extractKeyArray<<<grid_size, block_size>>>(
        thrust::raw_pointer_cast(d_key.data()),
        d_array, 
        size
    );

    // Step2: gen a seq to record the original index
    thrust::device_vector<int> d_seq(size);
    thrust::sequence(
        thrust::device, 
        d_seq.begin(), 
        d_seq.end()
    );
    
    // Step3: sort the seq array by the key array
    thrust::sort_by_key(
        thrust::device, 
        d_key.begin(), 
        d_key.end(), 
        d_seq.begin()
    );

    // Step4: rearrange the value array
    rearrangeValueArray<<<grid_size, block_size>>>(
        thrust::raw_pointer_cast(d_value.data()),
        d_array, 
        thrust::raw_pointer_cast(d_seq.data()),
        size
    );
}
```

This time, when `thrust/sort` is sorting our array, it only need to exchange the `key` array, which is much faster than the previous method.

