---
title: Multi RenderTarget
date: 2022-08-16
categories: [DirectX3D, DirectX3D]
tags: [directx3d]		# TAG는 반드시 소문자로 이루어져야함!
---

Multi RenderTarget(MRT)
====================

* 같은 Scene을 RenderTarget을 여러개를 사용해서 다른 Pixel 처리를 하는 방법

SwapChain이 윈도우의 핸들을 가지고 있으므로 SwapChain을 연결하지 않은 렌더타겟은 화면에 출력되지 않는다.<br>
RenderTarget을 여러 개로 사용하는 이유는 SwapChain이 아닌 다른 RenderTarget에 여러가지 정보를 담아서<br> 그 정보를 토대로 여러가지 기법을 사용할 수 있기 때문이다.


<br>

특징
==============================

* MRT는 DX11에서는 최대 8개의 RenderTarget과 하나의 DepthStencil를 사용가능하다.

* MRT 종류
  * Deffered
  * Light
  * Shadow
  
<br>

* MRT안에 Depth Stencil Texture가 없다면 (nullptr), 다른 렌더타겟의 Depth Stencil를 가져와서 사용한다.



<br><br>

CMRT.h
========================

        class CMRT :
            public CEntity
        {
        private:
            Ptr<CTexture>   m_arrRT[8]; // 최대 8개의 렌더타겟을 가질 수 있음
            int             m_iRTCount; // m_arrRT에 들어가 있는 텍스쳐의 개수

            Ptr<CTexture>   m_pDSTex;   // DepthStencil
            Vec4            m_arrClearColor[8]; // 인덱스 렌더타겟 Clear색상


        public:
            void Create(int _iRTCount, Ptr<CTexture>* _ppTex, Ptr<CTexture> _pDSTex);
            void SetClearColor(int _iCount, Vec4* _pColor)
            {
                for (int i = 0; i < _iCount; ++i)
                {
                    m_arrClearColor[i] = _pColor[i];
                }        
            }

            void OMSet();
            void Clear();


        public:
            CLONE_DISABLE(CMRT);

        public:
            CMRT();
            ~CMRT();
        };

<br>

CMRT.cpp
==========================================

        CMRT::CMRT()
            : m_arrRT{}
            , m_iRTCount(0)
            , m_pDSTex(nullptr)
            , m_arrClearColor{Vec4(0.f, 0.f, 0.f, 1.f)
            , Vec4(0.f, 0.f, 0.f, 1.f)
            , Vec4(0.f, 0.f, 0.f, 1.f)
            , Vec4(0.f, 0.f, 0.f, 1.f)
            , Vec4(0.f, 0.f, 0.f, 1.f)
            , Vec4(0.f, 0.f, 0.f, 1.f)
            , Vec4(0.f, 0.f, 0.f, 1.f)
            , Vec4(0.f, 0.f, 0.f, 1.f)}
        {
        }

        CMRT::~CMRT()
        {
        }

        void CMRT::Create(int _iRTCount, Ptr<CTexture>* _ppTex, Ptr<CTexture> _pDSTex)
        {
            m_iRTCount = _iRTCount;

            for (int i = 0; i < m_iRTCount; ++i)
            {
                m_arrRT[i] = _ppTex[i];
            }

            m_pDSTex = _pDSTex;
        }

        void CMRT::OMSet()
        {
            ID3D11RenderTargetView* arrView[8] = {};

            for (int i = 0; i < m_iRTCount; ++i)
            {
                arrView[i] = m_arrRT[i]->GetRTV().Get();
            }

            if (nullptr != m_pDSTex)
            {
                CONTEXT->OMSetRenderTargets(m_iRTCount, arrView, m_pDSTex->GetDSV().Get());
            }

            else
            {
                // MRT 에 DSTex 가 없는 경우, 장치에 있던걸 그대로 쓴다.

                // 스마트포인터를 사용하지 않으면 레퍼런스가 증가하기 때문에 Release를 사용해야함
                //ID3D11DepthStencilView* pDSView = nullptr;
                //CONTEXT->OMGetRenderTargets(0, nullptr, &pDSView);
                //pDSView->Release();
                
                ComPtr<ID3D11DepthStencilView> pDSView = nullptr;
                CONTEXT->OMGetRenderTargets(0, nullptr, pDSView.GetAddressOf());
                CONTEXT->OMSetRenderTargets(m_iRTCount, arrView, pDSView.Get());
            }	
        }

        void CMRT::Clear()
        {
            for (int i = 0; i < m_iRTCount; ++i)
            {
                CONTEXT->ClearRenderTargetView(m_arrRT[i]->GetRTV().Get(), m_arrClearColor[i]);
            }
            
            // DSTex가 없는 Deferred 일수도 있으므로 nullptr로 체크, 

            if(nullptr != m_pDSTex)
                CONTEXT->ClearDepthStencilView(m_pDSTex->GetDSV().Get(), D3D11_CLEAR_DEPTH | D3D11_CLEAR_STENCIL, 1.f, 0);
        }
