# Slabs-method for ray-AABB intersection

## Basic Ideas

Physically, ray is one point trveling from a start point to a direction. AABB is a box in 3D space. When we want to know if a ray intersects with an AABB, we can check if the ray intersects with the box's 3 dimentions at **any same** time.

## Implementation

We calculate the time scale passing the X axis, then Y, check thier overlaps. And last, Z axis. 

```cpp
bool RayAABBIntersect(const Ray& ray, const AABB& aabb) {
    float tmin = (aabb.min.x - ray.origin.x) / ray.direction.x;
    float tmax = (aabb.max.x - ray.origin.x) / ray.direction.x;

    // get original timescale (tmin, tmax)
    if (tmin > tmax) std::swap(tmin, tmax);

    float tymin = (aabb.min.y - ray.origin.y) / ray.direction.y;
    float tymax = (aabb.max.y - ray.origin.y) / ray.direction.y;

    if (tymin > tymax) std::swap(tymin, tymax);

    // cut this ray if X-Y axises has no intersection
    if ((tmin > tymax) || (tymin > tmax)) return false;

    // update the time scale (X-Y)
    if (tymin > tmin) tmin = tymin;
    if (tymax < tmax) tmax = tymax;

    float tzmin = (aabb.min.z - ray.origin.z) / ray.direction.z;
    float tzmax = (aabb.max.z - ray.origin.z) / ray.direction.z;

    if (tzmin > tzmax) std::swap(tzmin, tzmax);

    if ((tmin > tzmax) || (tzmin > tmax)) return false;

    return true;
}
```

## Advantages

Slabs-method only uses compare and swap operations, which is very fast.


[Go Back to HomePage](../../index.md)
