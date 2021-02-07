# Cluster笔记

## Tips

1. ClusterCB_NearK 	=>	$1+\frac{2tanθ}{Sy}$

2. ClusterCB_Size	=>	单块像素长宽

3. 

   1. LightList是当前待计算的场景内灯光序号列表
   2. LightInfo是当前待计算的场景内灯光信息列表
   3. LightIndexList是生效的灯光序号列表（序号存在重复）
   4. LightCount是每个cluster生效的灯光数量
   5. LightIndexStartOffset是每个cluster中生效的灯光在LightIndexList的起始序号

   

## 结构

 1.  LightData

     ##### float4 position

     ​	xyz:  light.localToWorldMatrix.GetColumn(3);

     ​	w:

     ```
     float outerRad = Mathf.Deg2Rad * 0.5f * light.spotAngle;
     float outerCos = math.cos(outerRad);
     float outerSin = math.sin(outerRad);
     float size = light.range / outerCos;
     if (light.spotAngle > 90)
     {
         v.w = outerSin * size;
     }
     else
     {
         v.w = size * 0.5f / outerCos;
     }
     ```

     ##### float4 color

     ##### float4 attenuation	//xy-> directional   zw->spot   default(0,1,0,1)

     ```
     float lightRangeSqr = light.range * light.range;
     float fadeStartDistanceSqr = 0.8f * 0.8f * lightRangeSqr;
     float fadeRangeSqr = (fadeStartDistanceSqr - lightRangeSqr);
     float cosOuterAngle = Mathf.Cos(Mathf.Deg2Rad * light.spotAngle * 0.5f);
     if (light.innerSpotAngle != 0)
         cosInnerAngle = Mathf.Cos(light.innerSpotAngle * Mathf.Deg2Rad * 0.5f);
     else
         cosInnerAngle = Mathf.Cos((2.0f * Mathf.Atan(Mathf.Tan(light.spotAngle * 0.5f * Mathf.Deg2Rad) * (64.0f - 18.0f) / 64.0f)) * 0.5f);
     
     float smoothAngleRange = Mathf.Max(0.001f, cosInnerAngle - cosOuterAngle);
     ```

     ​	__x:__	oneOverLightRangeSqr =  1.0f / Mathf.Max(0.0001f, light.range * light.range);

     ​	__y:__	lightRangeSqrOverFadeRangeSqr = light.range * light.range / (fadeStartDistanceSqr - lightRangeSqr)

     ​	__z:__	invAngleRange = 1/smoothAngleRange;

     ​	__w:__	add = -cosOuterAngle * invAngleRange;

     ##### float4 spotDirection = Vector4(-dir.x, -dir.y, -dir.z, 0.0f);

     ```
     Vector4 dir = light.localToWorldMatrix.GetColumn(2).normalized;
     ```

     ##### float4 occlusionProbeChannels = (0, 1, -1, -1)

     作用未知

     

## 公式

1. 指数对数转换公式    a^b^ = N;  log~a~ N= b

   








# 没看懂

LightSystem的Spotlight 的position.w的计算公式

​	

# 可能的优化方案

SpotLight的Cluster方案

[Optimizing spotlight intersection in tiled/clustered light culling (simoncoenen.com)](https://simoncoenen.com/blog/programming/graphics/SpotlightCulling.html)

```
bool SpotlightVsAABB(Spotlight spotlight, AABB aabb)
{
    float sphereRadius = dot(aabb.extents, aabb.extents);
    float3 v = aabb.venter - spotlight.position;
    float lenSq = dot(v, v);
    float v1Len = dot(v, spotlight.direction);
    float distanceClosestPoint = cos(spotlight.angle) * sqrt(lenSq - v1Len * v1Len) - v1Len * sin(spotlight.angle);
    bool angleCull = distanceClosestPoint > sphereRadius;
    bool frontCull = v1Len > sphereRadius + spotlight.range;
    bool backCull = v1Len < -sphereRadius;
    return !(angleCull || frontCull || backCull);
}
```