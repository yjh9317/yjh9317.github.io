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
    * 텍스쳐 기존색상을 가지고 있는 렌더타겟
    * 텍스쳐의 픽셀이 자기의 노말벡터를 RGB에 저장하고 있는 렌더타겟
    * 텍스쳐의 픽셀의 자기의 좌표정보를 RGB에 저장하고 있는 렌더타겟
  * Light
    * 광원정보
  * Shadow
    * 그림자


<br><br>

Forward와 Deffered
==============================
  * Forward (단일 렌더타겟)
    * 하나의 RenderTarget으로 한번에 렌더링해서 SwapChain의 RenderTarget으로 설정하는 방식<br><br>
  * Deferred (다중 렌더타겟 , 지연렌더링이라고도 함)
    * 별도의 RenderTarget Texture를 생성해서 각각 렌더링을 하고 메인 RenderTarget <br>(SwapChain의 RenderTarget)에 복사하는 방식


<br><br>


헤더파일
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

            void OMSet();
            void Clear();


        public:
            CLONE_DISABLE(CMRT);

        public:
            CMRT();
            ~CMRT();
        };

<br>

코드
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
            // 렌더타겟 갯수
            m_iRTCount = _iRTCount;

            // RenderTarget 세팅
            for (int i = 0; i < m_iRTCount; ++i)
            {
                m_arrRT[i] = _ppTex[i];
            }
            
            //Depth Stencil 세팅
            m_pDSTex = _pDSTex;
        }

        void CMRT::OMSet()
        {
            // RenderTargetView :  렌더링 파이프라인의 출력을 받을 자원을 연결하는 데 사용
            // RenderTarget은 RendertargetView를 통해 연결
            ID3D11RenderTargetView* arrView[8] = {};

            for (int i = 0; i < m_iRTCount; ++i)
            {
                arrView[i] = m_arrRT[i]->GetRTV().Get();
            }

            //SwapChain에 렌더타겟 세팅
            CONTEXT->OMSetRenderTargets(m_iRTCount, arrView, m_pDSTex->GetDSV().Get());
        }

        void CMRT::Clear()
        {
            for (int i = 0; i < m_iRTCount; ++i)
            {
                CONTEXT->ClearRenderTargetView(m_arrRT[i]->GetRTV().Get(), m_arrClearColor[i]);
            }
            
            CONTEXT->ClearDepthStencilView(m_pDSTex->GetDSV().Get(), D3D11_CLEAR_DEPTH | D3D11_CLEAR_STENCIL, 1.f, 0);
        }
