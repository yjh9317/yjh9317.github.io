---
title: VertexBuffer
date: 2022-06-03
categories: [DirectX, VertexBuffer]
tags: [directx]		# TAG는 반드시 소문자로 이루어져야함!
---



VertexBuffer
=============
* 정점의 정보를 저장해 두는 버퍼
* Vertex는 위치를 가지는 정점으로, 이 정점들을 이용하여 오브젝트의 형태를 정한다.
* Vertex들이 모여서 Direct3D의 Primitive에 의하여 도형(주로 삼각형)으로 만들어 3D 모델(Mesh)을 형성함
* Vertex의 물리적인 특성으로써 위치만 존재하고 크기는 무시됨
* Direct3D에서 사용되는 Vertex는 위치 값 이외에도 색상이나 텍스쳐 좌표를 가질 수 있는 다양한 부가적인 특성을 가지고 있음


<br><br>

버퍼를 만들때 사용할 변수
======================

	// 정점(Vertex) -> 폴리곤을 구성하기위한 단위 ,폴리곤의 단위는 일반적으로 삼각형
	// ID3D11Buffer는 Ram에서 생성되고 Gpu의 메모리를 관리하는 관리자급 객체란 느낌
	ComPtr<ID3D11Buffer>			g_pVB;
	
    // 쉐이더,픽셀 컴파일 실패시 ,실패 원인을 저장할 Blob(문자열 덩어리를 저장하는 객체)
    ComPtr<ID3DBlob>				g_pErrBlob;
    
    
    // 버텍스 쉐이더
    //	HLSL (High Level Shader Language)
    
    // g_pVSBlob에서 버텍스 쉐이더에 필요한 정보를 다 저장하고 그걸 바탕으로 g_pVS에 전달해서 버텍스 쉐이더를 생성
    // D12에서는 없어지고 g_pVSBlob만 있어도 사용 가능.
    
    ComPtr<ID3DBlob>			  	g_pVSBlob;  //버텍스 세이더 Blob
    ComPtr<ID3D11VertexShader>		g_pVS;      //버텍스 세이더

<br><br>

Vertex_Buffer
========================

		//버텍스 쉐이더에 사용할 정점 3개
		Vertex arrVtx[3] = {};
	
		//NDC를 사용해서 좌표 표현
		// NDC  : normalized device coordinates 의 약자로 (-1, -1)~(1, 1)의 종횡비 1:1 비율을 가진 2차원 좌표계를 의미한다.(쉽게 말해서 가로,세로의 길이가 2인 평면좌표계(사각형))
		//View Space의 모든 3차원 좌표는 이 2차원 좌표계로 사영된다.
	
		arrVtx[0].vPos = Vec3(0.f, 0.5f, 0.f);
		arrVtx[0].vColor = Vec4(1.f, 1.f, 1.f, 1.f);
	
		arrVtx[1].vPos = Vec3(0.5f, -0.5f, 0.f);
		arrVtx[1].vColor= Vec4(1.f, 1.f, 1.f, 1.f);
	
		arrVtx[2].vPos = Vec3(-0.5f, -0.5f, 0.f);
		arrVtx[2].vColor= Vec4(1.f, 1.f, 1.f, 1.f);
    
    
		// 정점 데이터를 저장할 버텍스 버퍼를 생성한다.
		D3D11_BUFFER_DESC tBufferDesc = {};
	
		//생성시킬 버퍼의 사이즈
		tBufferDesc.ByteWidth = sizeof(Vertex) * 3;
	
	
		//버퍼 생성 이후에도 , 버퍼의 내용을 수정 할 수 있는 옵션
		tBufferDesc.CPUAccessFlags = D3D11_CPU_ACCESS_WRITE;	
		tBufferDesc.Usage = D3D11_USAGE::D3D11_USAGE_DYNAMIC;
	
		//	정점을 저장하는 목적의 버퍼임을 알림,미리 어떤 데이터를 저장할 지 설정해야 최적화가 가능.
		tBufferDesc.BindFlags = D3D11_BIND_FLAG::D3D11_BIND_VERTEX_BUFFER;	
		tBufferDesc.MiscFlags = 0;
		tBufferDesc.StructureByteStride = 0;
		
		
	
		//초기 데이터를 넘겨주기 위한 정보 구조체
		D3D11_SUBRESOURCE_DATA tSubDesc = {};
		tSubDesc.pSysMem = arrVtx;// 시스템 메모리의 시작주소 
		
		//시작주소로부터 크기(하나단위)는 위에 ByteWidth에서 미리 잡아놓았기 때문에 알려주지 않아도 알아서 잡아준다.
	
		//점 3개를 버텍스 버퍼(gpu메모리)에 넣고 g_pVB(버텍스버퍼)에서 관리
		DEVICE->CreateBuffer(&tBufferDesc, &tSubDesc, g_pVB.GetAddressOf());
    


<br><br><br>
    
Vertex_Shader
=====================

		// 정점을 이용한 세이더,
		// Vertex Shader 컴파일

		UINT iFlag = 0;

		#ifdef  _DEBUG
		iFlag = D3DCOMPILE_DEBUG;
		#endif


		wstring strContentPath = CPathMgr::GetInst()->GetContentPath();
		

		
		
		//버텍스 쉐이더(HLSL) 컴파일

		// 파일경로 , nullptr, 매크로, 컴파일할 함수명 , 버텍스 쉐이더 버전,플래그 두개,컴파일값을 저장할 객체 (Blob)주소,
		에러가 났을 경우 문자열을 저장할 객체(ErrBlob) 주소

		HRESULT hr = D3DCompileFromFile(wstring(strContentPath+L"shader\\test.fx").c_str(), nullptr
			,D3D_COMPILE_STANDARD_FILE_INCLUDE,"VS_Test","vs_5_0", iFlag,0,g_pVSBlob.GetAddressOf(), g_pErrBlob.GetAddressOf());

			
		//컴파일을 실패했다면
		if (FAILED(hr))
		{
			MessageBoxA(nullptr, (char*)g_pErrBlob->GetBufferPointer(),"Shader Compile Failed!!",MB_OK);
			assert(nullptr);
		}

		//컴파일 된 코드로 VertexShader 객체 만들기
		//Blob이 관리하고있는 메모리 버퍼의 시작 주소 ,길이 , nullptr ,목적지로 저장할 버텍스 쉐이더
		DEVICE->CreateVertexShader(g_pVSBlob->GetBufferPointer(), g_pVSBlob->GetBufferSize(), nullptr,g_pVS.GetAddressOf());

