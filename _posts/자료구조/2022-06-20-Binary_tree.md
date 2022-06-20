---
published: true
title: Binary Tree
date: 2022-06-20
categories: [자료구조, Tree]
tags: [data_structure]		# TAG는 반드시 소문자로 이루어져야함!
---


이진 트리
========================
 * 트리의 모든 노드를 차수 2 이하로 제한하여 전체 트리의 차수가 2 이하가 되도록 정의한 트리
 * 이진 트리의 자식노드는 왼쪽 자식노드와 오른쪽 자식노드만 있다.

<br><br>

* 이진트리의 특성
  * 노드가 n개인 이진 트리는 항상 간선이 (n-1)개이다
  * 높이가 h인 이진 트리가 가질 수 있는 노드 개수는 최소(h+1)개이며, 최대(2^h-1)개이다.

<br>

* 높이가 3인 최소 노드를 갖는 트리(왼쪽)와 최대 노드를 갖는 트리(오른쪽)
<img src="./../../assets/img/Binray%20Tree.png">

<br><br>

* 이진 트리의 종류
  * 포화 이진트리
    * 모든 레벨에 노드가 꽉 차 더 이상 노드를 추가할 수 없는 트리
    <img src="./../../assets/img/Full%20Binary%20Tree.png" ><br><br><br><br><br>
  * 완전 이진 트리
    * 높이가 h이고 노드 수가 n일때, 노드 위치가 포화 이진 트리에서의 노드 1번부터 n번까지 위치가 완전히 일치하는 트리
    <img src="./../../assets/img/Compelete%20Binray%20Tree.jpg"><br><br><br><br><br>

  * 편향 이진 트리
    * 높이가 h일 때, h+1개의 노드를 가지면서 모든 노드가 왼쪽이나 오른쪽중 한 방향으로만 서브 트리를 가지고 있는 트리
    <img src="./.../../../../assets/img/Skewed%20Binary%20Tree.png">

<br><br><br><br><br>

이진 트리의 구성
========================
 * 배열을 이용한 이진트리와 구조체를 이용한 이진트리가 있다
 
 <br><br>

 * 배열로 구현한 이진트리
   * 1차원 배열로 높이가 h인 포화 이진트리의 노드 번호를 배열의 인덱스로 사용한다.
   * 인덱스 관계  
  
   |노드|인덱스|성립조건|
   |----|----|--------|
   |노드 i의 부모 노드| i/2 | i>1|
   |노드 i의 왼쪽 자식 노드|2 x i| (2 x i) ≤ n|
   |노드 i의 오른쪽 자식 노드|(2 x i) + 1|(2 x i) + 1 ≤ n|
   |루트 노드| 1 | n>0|

<br><br><br>

 * 포인터가 포함된 노드(구조체)로 구현한 이진트리
   * 데이터를 저장하는 변수와 왼쪽 자식노드를 연결하는 포인터,오른쪽 자식노드를 연결하는 포인터로 구성
  
<br>

      typedef struct treeNode{
        char data;
        struct treeNode *left;
        struct treeNode *right;
      } treeNode;


<br>

이진 트리의 순회
===========================
 * 순회란 모든 원소를 빠트리거나 중복하지 않고 처리하는 연산
 * 선형 자료구조와 달리 비선형 계층 구조인 트리는 현재 노드를 처리한 후에 어떤 노드를 처리할지 결정하는 기준을 정해놓은 연산이 필요하다

<br>

 * 이진트리의 순회 종류
   * 전위 순회
     * 현재노드 처리 -> 왼쪽 자식노드 처리 -> 오른쪽 자식노드 처리
   * 중위 순회
     * 왼쪽 자식노드 처리 -> 현재 노드 처리 -> 오른쪽 자식노드 처리
   * 후위 순회
     * 왼쪽 자식 노드 처리 -> 오른쪽 자식노드 처리 -> 현재 노드 처리

<br>

* 순회 Code


      // 이진 트리에 대한 전위 순회 연산
      void preorder(treeNode* root) {
        if (root) {
          printf("%c", root->data);   //현재 노드 처리
          preorder(root->left);       //왼쪽 자식노드 처리
          preorder(root->right);	    //오른쪽 자식노드 처리
        }
      }

      // 이진 트리에 대한 중위 순회 연산
      void inorder(treeNode* root) {
        if (root) {
          inorder(root->left);        //왼쪽 자식노드 처리
          printf("%c", root->data);   //현재 노드 처리
          inorder(root->right);       //오른쪽 자식노드 처리
        }
      }

      // 이진 트리에 대한 후위 순회 연산
      void postorder(treeNode* root) {
        if (root) {
          postorder(root->left);      //왼쪽 자식노드 처리
          postorder(root->right);     //오른쪽 자식노드 처리
          printf("%c", root->data);   //현재 노드 처리
        }
      }