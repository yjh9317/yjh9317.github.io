---
title: CubeMesh && SphereMesh
date: 2022-08-10
categories: [DirectX3D, DirectX3D]
tags: [directx3d]		# TAG는 반드시 소문자로 이루어져야함!
---
    
# **큐브 메시 (Cube Mesh)**

* 큐브는 6개의 면을 가진 정육면체. 각 면은 사각형이며, 두 개의 삼각형으로 구성

## **큐브 메시의 정점 구성**

* 단순히 생각하면 큐브는 8개의 고유한 꼭짓점을 가짐
* 하지만, 각 면(face)이 고유한 법선 벡터(normal vector)를 가져야 하고, 텍스처 UV 좌표가 면마다 다르게 매핑될 수 있기 때문에, 각 면에 속한 정점들은 별도로 정의하는 것이 일반적
* 이렇게 하면 각 면의 평평한 셰이딩(flat shading)이 가능해지고, 텍스처를 각 면에 올바르게 입힐 수 있음
* 큐브는 6개의 면을 가지고, 각 면은 4개의 정점으로 구성되므로, 총 6 * 4 = 24개의 정점이 필요
* 각 정점은 위치(Position), 색상(Color), UV 좌표(Texture Coordinate), 법선(Normal) 등의 정보를 가짐

<br>

## **정점 데이터 정의 (C++ 예시)**

* 다음은 24개의 정점을 정의하는 코드의 일부입니다. 각 면(윗면, 아랫면, 왼쪽, 오른쪽, 뒷면, 앞면)마다 4개의 정점이 있으며, 각 정점은 해당 면의 법선 벡터를 가짐

```c++
// (Vtx 구조체는 이전 메시 생성 가이드의 정의를 따른다고 가정)
// struct Vtx { Vec3 vPos; Vec4 vColor; Vec2 vUV; Vec3 vNormal; /* Vec3 vTangent, vBinormal; */ };

std::vector<Vtx> vecVertices;
Vtx v;
float halfSize = 0.5f; // 큐브의 한 변 길이의 절반

// 윗면 (Y = +halfSize, Normal = (0, 1, 0))
v.vPos = Vec3(-halfSize, halfSize, halfSize);  v.vColor = Vec4(1,1,1,1); v.vUV = Vec2(0,0); v.vNormal = Vec3(0,1,0); vecVertices.push_back(v); // 0
v.vPos = Vec3( halfSize, halfSize, halfSize);  v.vColor = Vec4(1,1,1,1); v.vUV = Vec2(1,0); v.vNormal = Vec3(0,1,0); vecVertices.push_back(v); // 1
v.vPos = Vec3( halfSize, halfSize, -halfSize); v.vColor = Vec4(1,1,1,1); v.vUV = Vec2(1,1); v.vNormal = Vec3(0,1,0); vecVertices.push_back(v); // 2
v.vPos = Vec3(-halfSize, halfSize, -halfSize); v.vColor = Vec4(1,1,1,1); v.vUV = Vec2(0,1); v.vNormal = Vec3(0,1,0); vecVertices.push_back(v); // 3

// 아랫면 (Y = -halfSize, Normal = (0, -1, 0)) - UV 좌표는 윗면과 동일하게 설정하거나 다르게 할 수 있음
v.vPos = Vec3(-halfSize, -halfSize, -halfSize); v.vColor = Vec4(1,0,0,1); v.vUV = Vec2(0,0); v.vNormal = Vec3(0,-1,0); vecVertices.push_back(v); // 4
v.vPos = Vec3( halfSize, -halfSize, -halfSize); v.vColor = Vec4(1,0,0,1); v.vUV = Vec2(1,0); v.vNormal = Vec3(0,-1,0); vecVertices.push_back(v); // 5
v.vPos = Vec3( halfSize, -halfSize,  halfSize); v.vColor = Vec4(1,0,0,1); v.vUV = Vec2(1,1); v.vNormal = Vec3(0,-1,0); vecVertices.push_back(v); // 6
v.vPos = Vec3(-halfSize, -halfSize,  halfSize); v.vColor = Vec4(1,0,0,1); v.vUV = Vec2(0,1); v.vNormal = Vec3(0,-1,0); vecVertices.push_back(v); // 7

// 왼쪽 면 (X = -halfSize, Normal = (-1, 0, 0))
v.vPos = Vec3(-halfSize,  halfSize,  halfSize); v.vColor = Vec4(0,1,0,1); v.vUV = Vec2(0,0); v.vNormal = Vec3(-1,0,0); vecVertices.push_back(v); // 8
v.vPos = Vec3(-halfSize,  halfSize, -halfSize); v.vColor = Vec4(0,1,0,1); v.vUV = Vec2(1,0); v.vNormal = Vec3(-1,0,0); vecVertices.push_back(v); // 9
v.vPos = Vec3(-halfSize, -halfSize, -halfSize); v.vColor = Vec4(0,1,0,1); v.vUV = Vec2(1,1); v.vNormal = Vec3(-1,0,0); vecVertices.push_back(v); // 10
v.vPos = Vec3(-halfSize, -halfSize,  halfSize); v.vColor = Vec4(0,1,0,1); v.vUV = Vec2(0,1); v.vNormal = Vec3(-1,0,0); vecVertices.push_back(v); // 11

// 오른쪽 면 (X = +halfSize, Normal = (1, 0, 0))
v.vPos = Vec3( halfSize,  halfSize, -halfSize); v.vColor = Vec4(0,0,1,1); v.vUV = Vec2(0,0); v.vNormal = Vec3(1,0,0); vecVertices.push_back(v); // 12
v.vPos = Vec3( halfSize,  halfSize,  halfSize); v.vColor = Vec4(0,0,1,1); v.vUV = Vec2(1,0); v.vNormal = Vec3(1,0,0); vecVertices.push_back(v); // 13
v.vPos = Vec3( halfSize, -halfSize,  halfSize); v.vColor = Vec4(0,0,1,1); v.vUV = Vec2(1,1); v.vNormal = Vec3(1,0,0); vecVertices.push_back(v); // 14
v.vPos = Vec3( halfSize, -halfSize, -halfSize); v.vColor = Vec4(0,0,1,1); v.vUV = Vec2(0,1); v.vNormal = Vec3(1,0,0); vecVertices.push_back(v); // 15

// 뒷면 (Z = +halfSize, Normal = (0, 0, 1)) - UV 방향은 보는 방향에 따라 조정
v.vPos = Vec3( halfSize,  halfSize, halfSize); v.vColor = Vec4(1,1,0,1); v.vUV = Vec2(0,0); v.vNormal = Vec3(0,0,1); vecVertices.push_back(v); // 16
v.vPos = Vec3(-halfSize,  halfSize, halfSize); v.vColor = Vec4(1,1,0,1); v.vUV = Vec2(1,0); v.vNormal = Vec3(0,0,1); vecVertices.push_back(v); // 17
v.vPos = Vec3(-halfSize, -halfSize, halfSize); v.vColor = Vec4(1,1,0,1); v.vUV = Vec2(1,1); v.vNormal = Vec3(0,0,1); vecVertices.push_back(v); // 18
v.vPos = Vec3( halfSize, -halfSize, halfSize); v.vColor = Vec4(1,1,0,1); v.vUV = Vec2(0,1); v.vNormal = Vec3(0,0,1); vecVertices.push_back(v); // 19

// 앞면 (Z = -halfSize, Normal = (0, 0, -1)) - UV 방향은 보는 방향에 따라 조정
v.vPos = Vec3(-halfSize,  halfSize, -halfSize); v.vColor = Vec4(1,0,1,1); v.vUV = Vec2(0,0); v.vNormal = Vec3(0,0,-1); vecVertices.push_back(v); // 20
v.vPos = Vec3( halfSize,  halfSize, -halfSize); v.vColor = Vec4(1,0,1,1); v.vUV = Vec2(1,0); v.vNormal = Vec3(0,0,-1); vecVertices.push_back(v); // 21
v.vPos = Vec3( halfSize, -halfSize, -halfSize); v.vColor = Vec4(1,0,1,1); v.vUV = Vec2(1,1); v.vNormal = Vec3(0,0,-1); vecVertices.push_back(v); // 22
v.vPos = Vec3(-halfSize, -halfSize, -halfSize); v.vColor = Vec4(1,0,1,1); v.vUV = Vec2(0,1); v.vNormal = Vec3(0,0,-1); vecVertices.push_back(v); // 23
```

* UV 좌표: 위 예시에서는 각 면의 UV를 (0,0), (1,0), (1,1), (0,1) (또는 유사한 방식)로 설정하여 해당 면 전체에 텍스처가 매핑되도록 해야 함.
  * 원본 코드의 모든 UV가 (0,0)인 것은 텍스처링 시 문제가 됨.
  * 여기서는 예시로 각 면의 첫 번째 정점을 (0,0)으로 시작하도록 표현.
  * 실제 텍스처 아틀라스나 UV 언래핑 방식에 따라 조정이 필요
* 법선 벡터: 각 면의 법선은 해당 면이 바라보는 방향을 나타냄 
  * (예: 윗면은 (0,1,0), 앞면은 (0,0,-1) - 왼손 좌표계 기준).

<br>

## **인덱스 데이터 정의 (C++ 예시)**

* 각 면(4개의 정점으로 구성된 사각형)은 두 개의 삼각형으로 나뉨. 총 6개의 면이 있으므로 12개의 삼각형이 필요하며, 각 삼각형은 3개의 인덱스를 가지므로 총 36개의 인덱스가 필요
* 이 인덱스 순서(와인딩 순서)는 컬링(culling) 및 법선 방향에 중요.
  * Direct3D는 기본적으로 시계 방향(CW)을 앞면으로 간주

```c++
std::vector<UINT> vecIndices;
for (int i = 0; i < 6; ++i) // 6개의 면에 대해 반복
{
    UINT baseIndex = i * 4; // 각 면의 시작 정점 인덱스
    // 첫 번째 삼각형 (예: 0-1-2)
    vecIndices.push_back(baseIndex + 0);
    vecIndices.push_back(baseIndex + 1);
    vecIndices.push_back(baseIndex + 2);
    // 두 번째 삼각형 (예: 0-2-3)
    vecIndices.push_back(baseIndex + 0);
    vecIndices.push_back(baseIndex + 2);
    vecIndices.push_back(baseIndex + 3);
}
```

<br>

# **구 메시 (Sphere Mesh)**

* 구 메시는 `극점(pole)`과 `위도선(stack, 수평 링)`, `경도선(slice, 수직 분할)`을 이용하여 생성

## **구 메시의 원리**

### **1. 극점 정의**

* 구의 가장 위쪽(북극)과 가장 아래쪽(남극)에 정점을 하나씩 정의

### **2. 스택(Stack) 생성**

* 북극과 남극 사이에 여러 개의 수평 원형 선(스택 또는 링)을 만듦.
  * 각 스택은 특정 위도에 해당

### **3. 슬라이스(Slice) 분할**

* 각 스택(링)을 여러 개의 수직 선분(슬라이스)으로 나누어 정점들을 생성.
  * 각 슬라이스는 특정 경도에 해당

### **4. 삼각형 구성**

* 극점과 첫 번째/마지막 스택의 정점들을 연결하여 부채꼴 모양의 삼각형들을 만듦

* 인접한 스택 사이의 정점들을 연결하여 사각형 띠를 만들고, 각 사각형을 두 개의 삼각형으로 나눔

* 법선 벡터: 원점이 중심인 구의 표면에서 특정 정점의 법선 벡터는 원점에서 해당 정점을 향하는 방향 벡터를 정규화한 것과 같음 (normalize(vertexPosition)).

## **정점 데이터 정의 (C++ 예시)**

```c++
std::vector<Vtx> vecVertices;
std::vector<UINT> vecIndices;
Vtx v;

float fRadius = 0.5f;
UINT iStackCount = 20; // 구를 감싸는 수평 링의 개수 (극점 제외)
UINT iSliceCount = 20; // 각 링을 구성하는 수직 분할 개수

// 1. 북극점 (Top Pole)
v.vPos = Vec3(0.0f, fRadius, 0.0f);
v.vColor = Vec4(1.f, 1.f, 1.f, 1.f);
v.vUV = Vec2(0.5f, 0.0f); // UV 좌표의 상단 중앙
v.vNormal = Vec3(0.f, 1.f, 0.f);
// (구의 경우 탄젠트/바이노멀은 조금 더 복잡하게 계산되거나, 셰이더에서 필요시 생성)
// 간단한 예시: v.vTangent = Vec3(1.f, 0.f, 0.f); v.vBinormal = Vec3(0.f, 0.f, 1.f);
vecVertices.push_back(v);

// 2. 몸통 (Body Stacks and Slices)
float phiStep = XM_PI / (float)(iStackCount + 1); // 극점에서 극점까지의 각도 단계
float thetaStep = XM_2PI / (float)iSliceCount;   // 한 바퀴를 도는 각도 단계

for (UINT i = 1; i <= iStackCount; ++i) // 스택 루프 (북극 바로 아래부터 남극 바로 위까지)
{
    float phi = i * phiStep; // 현재 스택의 수직 각도 (Y축 기준)

    for (UINT j = 0; j <= iSliceCount; ++j) // 슬라이스 루프 (한 바퀴 + 중복 정점 1개_UV 끊김 처리용)
    {
        float theta = j * thetaStep; // 현재 슬라이스의 수평 각도

        // 구면 좌표계를 사용하여 정점 위치 계산
        v.vPos.x = fRadius * sinf(phi) * cosf(theta);
        v.vPos.y = fRadius * cosf(phi);
        v.vPos.z = fRadius * sinf(phi) * sinf(theta);

        v.vColor = Vec4(1.f, 1.f, 1.f, 1.f);
        v.vUV = Vec2(theta / XM_2PI, phi / XM_PI); // (j / iSliceCount, i / (iStackCount+1))
        
        v.vNormal = normalize(v.vPos); // 원점 중심 구의 법선은 위치 벡터 정규화

        // 탄젠트, 바이노멀 계산 (정확한 계산은 셰이더 또는 모델링 도구에서)
        // 예시: (phi에 대한 편미분 -> 탄젠트, theta에 대한 편미분 -> 바이노멀 근사)
        // v.vTangent = Vec3(-fRadius * sinf(phi) * sinf(theta), 0, fRadius * sinf(phi) * cosf(theta));
        // v.vTangent.Normalize();
        // v.vBinormal = cross(v.vNormal, v.vTangent); // 오른손 좌표계 기준, 또는 반대
        // v.vBinormal.Normalize();
        
        vecVertices.push_back(v);
    }
}

// 3. 남극점 (Bottom Pole)
v.vPos = Vec3(0.0f, -fRadius, 0.0f);
v.vColor = Vec4(1.f, 1.f, 1.f, 1.f);
v.vUV = Vec2(0.5f, 1.0f); // UV 좌표의 하단 중앙
v.vNormal = Vec3(0.f, -1.f, 0.f);
// v.vTangent = Vec3(1.f, 0.f, 0.f); v.vBinormal = Vec3(0.f, 0.f, -1.f);
vecVertices.push_back(v);
```

## **인덱스 데이터 정의 (C++ 예시)**

* UV 좌표 및 중복 정점: 구의 몸통 부분에서 각 링(스택)의 시작점과 끝점은 3D 공간에서는 동일한 위치이지만, 텍스처 UV 좌표에서는 U값이 0과 1로 달라야 텍스처가 올바르게 감싸짐.
  * 이를 위해 각 링마다 iSliceCount + 1개의 정점을 생성하고, j가 0부터 iSliceCount까지 반복

* 인덱싱: 극점, 몸통, 다른 극점을 연결하는 인덱스를 정확히 계산하는 것이 중요.
  * 정점 배열의 인덱스 오프셋을 주의 깊게 다뤄야 함

```c++
// 1. 북극 캡 인덱스 구성
// 첫 번째 정점(vecVertices[0])이 북극점
for (UINT j = 0; j < iSliceCount; ++j)
{
    vecIndices.push_back(0);                                  // 북극점
    vecIndices.push_back(j + 2);                              // 첫 번째 링의 (j+1)번째 정점
    vecIndices.push_back(j + 1);                              // 첫 번째 링의 j번째 정점
}

// 2. 몸통 인덱스 구성
// 각 스택 링은 (iSliceCount + 1)개의 정점을 가짐 (UV 끊김 처리를 위해 시작점 중복)
for (UINT i = 0; i < iStackCount - 1; ++i) // 스택 링 사이를 연결
{
    for (UINT j = 0; j < iSliceCount; ++j)
    {
        // 현재 링의 시작 인덱스: 1 (북극점 다음) + i * (iSliceCount + 1)
        // 다음 링의 시작 인덱스: 1 (북극점 다음) + (i + 1) * (iSliceCount + 1)
        UINT currentRowStart = 1 + i * (iSliceCount + 1);
        UINT nextRowStart = 1 + (i + 1) * (iSliceCount + 1);

        // 사각형을 두 개의 삼각형으로 나눔
        // 삼각형 1: (현재 링 j, 다음 링 j, 다음 링 j+1)
        vecIndices.push_back(currentRowStart + j);
        vecIndices.push_back(nextRowStart + j);
        vecIndices.push_back(nextRowStart + j + 1);

        // 삼각형 2: (현재 링 j, 다음 링 j+1, 현재 링 j+1)
        vecIndices.push_back(currentRowStart + j);
        vecIndices.push_back(nextRowStart + j + 1);
        vecIndices.push_back(currentRowStart + j + 1);
    }
}

// 3. 남극 캡 인덱스 구성
// 마지막 정점(vecVertices.back() 또는 vecVertices[vecVertices.size()-1])이 남극점
UINT bottomPoleIndex = (UINT)vecVertices.size() - 1;
// 마지막 링의 시작 인덱스: 1 (북극점 다음) + (iStackCount - 1) * (iSliceCount + 1)
UINT lastRingStartIndex = 1 + (iStackCount - 1) * (iSliceCount + 1);
for (UINT j = 0; j < iSliceCount; ++j)
{
    vecIndices.push_back(bottomPoleIndex);                 // 남극점
    vecIndices.push_back(lastRingStartIndex + j);          // 마지막 링의 j번째 정점
    vecIndices.push_back(lastRingStartIndex + j + 1);      // 마지막 링의 (j+1)번째 정점
}
```