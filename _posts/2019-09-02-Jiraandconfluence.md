---
layout: post
title: Jira와 Confluence
author: Doosil
date: '2019-09-02 00:00:00 +0530'
category: Devops
summary: Devops
tag_name: Devops

---

<br>

# JIRA와 Confluence

<br>

- Jira와 Confluence의 간략한 소개와 차이점을 알아보자.

<br>

## 1. JIRA란?

<br>

### 1. JIRA 개념

- 호주 회사 Atlassian에서 Bug 및 Issue를 트래킹하고 프로젝트를 관리할 수 있도록 만든 Tool
- 대부분 Agile 방식 프로젝트 관리를 위해 설계되었다.
- Dashboard에서는 무슨 Issue가 어떤 사람에게 할당되었는지를 확인할 수 있다.

<br>

### 2. JIRA workflow

1. Open Issue - Issue가 발생하면, 담당자에게 할당이 된다.
2. In Progress Issue - 담당자 이를 할당받고 작업을 시작했음을 "in progress"로 표시한다.
3. Resolved Issue - 담당자가 issue를 성공적으로 해결한 후 표시한다. 동시에 Issue 제기자나 다른 관련 된 사람에게 Issue를 할당할 수 있으며, 이를 검증한 후 성공이면, issue가 close 되고 추가 변경사항이 필요하면 issue가 다시 열린다.
4. Reopened Issue - Issue에 대한 해결이 완벽히 되지 않았거나, 또 다른 Bug들이 발견 되었을 때, 이전 Issue 담당자나 다른 담당자에게 할당할 수 있다.
5. Close issue - Issue가 성공적으로 해결 된 것이 검증되면, Issue가 닫힌다. 일반적으로 Issue를 보고한 사람이 이 작업을 수행한다.

<br>

![](/assets/img/posts/jiraworkflow.PNG)

<br>



## 2. Confluence란?

<br>

### 1. Confluence 개념

- Confluence는 팀원들이 효율적으로 지식을 공유하고 협업할 수 있는 도구이다.
- 사용자들은 Blog 및 Page를 만들 수 있으며 이에 댓글을 달고 글을 편집할 수 있다.
- 로드맵, 체크리스트가 있는 메모, 지식 관련 문서 등을 중앙에서 관리가 가능하다.
- JIRA와 통합되도록 설계되었기 때문에, 서로 상호 작용이 가능하다.



<br>

## 3. JIRA와 Confluence 차이점



| **The Basis Of Comparison Between Jira vs Confluence** | **Confluence**                                               | **JIRA**                                                     |
| ------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Description**                                        | A Confluence is a collaboration tool that stores and organizes all of your information assets around the projects you’re doing in JIRA | JIRA is an issue management platform that allows teams to manage their issues throughout their entire lifecycle easily. It is highly customizable and can fit in any workflow we need. It is basically used in software development as a way to manage and track development efforts. |
| **PLATFORMS SUPPORTED**                                | Web, iPhone, Android                                         | Web, iPhone, Android                                         |
| **Typical Customers**                                  | Small, Midsize and Enterprise customers                      | Small, Midsize and Enterprise customers                      |
| **Pricing**                                            | Starting from $10 / month                                    | Starting from $10 / month                                    |
| **Access control**                                     | Supported                                                    | Not Supported                                                |
| **action management**                                  | Not Supported                                                | Supported                                                    |
| **active directory integration**                       | Integration not available                                    | Integration available                                        |
| **activity dashboard and tracking**                    | Available                                                    | Available                                                    |
| **Application integration**                            | Not Supported                                                | Supported                                                    |
| **assignment management**                              | Not Supported                                                | Supported                                                    |
| **calendar management**                                | Supported                                                    | Not Supported                                                |
| **content management**                                 | Supported                                                    | Not Supported                                                |
| **DOCUMENT MANAGEMENT**                                | Supported                                                    | Not Supported                                                |
| **DYNAMIC WORKFLOW**                                   | Not Supported                                                | Supported                                                    |
| **Project planning**                                   | Supported                                                    | Not supported                                                |
| **project management**                                 | Supported                                                    | Not supported                                                |
| **project time tracking**                              | Not supported                                                | Supported                                                    |
| **third-party integration**                            | Supported                                                    | Supported                                                    |
| **reporting and statistics**                           | Not supported                                                | Supported                                                    |
| **project workflow**                                   | Not available                                                | Available                                                    |

<br>

## 4. JIRA와 Confluence를 통합해야하는 이유

<br>

1. 팀원들 간에 email이나 드라이브 공유가 필요 없이 중앙에서 문서를 관리할 수 있으며, Release note, Requirement Specifications, Code review등도 직접 관리할 수 있다.
2. 팀 간 커뮤니케이션이 가능토록 하고, 비즈니스 이해 관계자는 팀 진행상황을 완전히 파악할 수 있다.
3. Confluence 페이지를 Jira로 가져올 수 있어서 개발자가 현재 작업중인 Taks와 관련된 문서를 쉽게 볼 수 있습니다. Confluence 및 Jira 페이지 간에 사용자마다 권한을 제한 할 수도 있다.
4. 비즈니스 팀은 보고에 관해 큰 이득을 가질 수 있다. JIRA에서 계속해서 프로젝트의 Issue가 계속 발생하지만 이를 계속 자세히 조사할 시간이나 여유가 없다. 따라서 JIRA 정보를 Confluence 보고서에 연결하면 비즈니스 팀은 JIRA 관련 많은 데이터를 간단한 사용자 인터페이스로 표시할 수 있다.





[참조 사이트](https://www.educba.com/jira-vs-confluence/) : https://www.educba.com/jira-vs-confluence



