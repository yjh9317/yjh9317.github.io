---
title: Cylinder Mesh
date: 2022-09-29
categories: [DirectX3D, DirectX3D]
tags: [directx3d]		# TAG는 반드시 소문자로 이루어져야함!
---

Cylinder Mesh
===============

	// Cylinder

	int stackCount = 20;
	iSliceCount = 20;

	//fUVXStep = 1.f / (float)iSliceCount;
	//fUVYStep = 0.f;

	float topRadius = 0.25f;
	float bottomRadius = 0.5f;

	float height = 1.f;

	float stackHeight = height / stackCount;
	// Amount to increment radius as we move up each stack
	// level from bottom to top.
	float radiusStep = (topRadius - bottomRadius) / stackCount;
	UINT ringCount = stackCount + 1;
	// Compute vertices for each stack ring starting at
	// the bottom and moving up.
	for (UINT i = 0; i < ringCount; ++i)
	{
		float y = -0.5f* height + i * stackHeight;
		float r = bottomRadius + i * radiusStep;
		// vertices of ring
		float dTheta = 2.0f * XM_PI / iSliceCount;
		for (UINT j = 0; j <= iSliceCount; ++j)
		{
			Vertex vertex;
			float c = cosf(j * dTheta);
			float s = sinf(j * dTheta);
			vertex.vPos = XMFLOAT3(r * c, y, r * s);
			vertex.vUV = Vec2((float)j / iSliceCount,1.0f - (float)i / stackCount);

			vertex.vTangent = XMFLOAT3(-s, 0.0f, c);
			float dr = bottomRadius - topRadius;
			XMFLOAT3 bitangent(dr * c, -1.f, dr * s);
			XMVECTOR T = XMLoadFloat3(&vertex.vTangent);
			XMVECTOR B = XMLoadFloat3(&bitangent);
			XMVECTOR N = XMVector3Normalize(XMVector3Cross(T, B));
			XMStoreFloat3(&vertex.vNormal, N);


			vecVtx.push_back(vertex);
		}
	}

	UINT ringVertexCount = iSliceCount + 1;
	// Compute indices for each stack.
	for (UINT i = 0; i < stackCount; ++i)
	{
		for (UINT j = 0; j < iSliceCount; ++j)
		{
			vecIdx.push_back(i * ringVertexCount + j);
			vecIdx.push_back((i + 1) * ringVertexCount + j + 1);
			vecIdx.push_back((i + 1) * ringVertexCount + j);
			
			vecIdx.push_back(i * ringVertexCount + j);
			vecIdx.push_back(i * ringVertexCount + j + 1);
			vecIdx.push_back((i + 1) * ringVertexCount + j + 1);
			
		}
	}
	// Top
	fRadius = 0.5f;

	v.vPos = Vec3(0.f, fRadius, 0.f);
	v.vUV = Vec2(0.5f, 0.f);
	v.vColor = Vec4(1.f, 1.f, 1.f, 1.f);
	v.vNormal = v.vPos;
	v.vNormal.Normalize();
	v.vTangent = Vec3(1.f, 0.f, 0.f);
	v.vBinormal = Vec3(0.f, 0.f, 1.f);
	vecVtx.push_back(v);

	// Bottom
	v.vPos = Vec3(0.f, -fRadius, 0.f);
	v.vUV = Vec2(0.5f, 1.f);
	v.vColor = Vec4(1.f, 1.f, 1.f, 1.f);
	v.vNormal = v.vPos;
	v.vNormal.Normalize();

	v.vTangent = Vec3(1.f, 0.f, 0.f);
	v.vBinormal = Vec3(0.f, 0.f, -1.f);
	vecVtx.push_back(v);


	for (UINT j = 0; j < iSliceCount; ++j)
	{
		vecIdx.push_back(vecVtx.size() - 1);
		vecIdx.push_back(j + 1);
		vecIdx.push_back(j + 2);
	}

	for (UINT j = 0; j < iSliceCount; ++j)
	{
		vecIdx.push_back(vecVtx.size() - 2);
		vecIdx.push_back(ringVertexCount * (iSliceCount + 1) - j - 1);
		vecIdx.push_back(ringVertexCount * (iSliceCount + 1) - j - 2);
	}