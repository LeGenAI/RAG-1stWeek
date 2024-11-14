# Hateslop Week5

## Overview of metrics

- 메트릭은 AI 애플리케이션의 성능을 평가하기 위해 사용되는 정량적 지표입니다. 메트릭은 애플리케이션과 애플리케이션을 구성하는 개별 요소들이 주어진 테스트 데이터에 대해 얼마나 잘 수행되고 있는지를 평가하는 데 도움을 줍니다. 메트릭은 비교, 최적화, 그리고 애플리케이션 개발 및 배포 과정에서 의사 결정을 위한 수치적 근거를 제공합니다. 메트릭은 다음과 같은 측면에서 중요합니다:
    1. 구성 요소 선택: 메트릭은 AI 애플리케이션의 다양한 구성 요소, 예를 들어 LLM(대형 언어 모델), 검색기(Retriever), 에이전트 구성 등의 성능을 사용자 데이터와 비교하여 다양한 옵션 중에서 최적의 것을 선택하는 데 사용할 수 있습니다. 
    2. 오류 진단 및 디버깅: 메트릭은 애플리케이션의 어떤 부분에서 오류가 발생하거나 성능이 저하되는지를 식별하는 데 도움을 주어 디버깅 및 개선을 용이하게 합니다. 
    3. 지속적인 모니터링 및 유지보수: 메트릭은 AI 애플리케이션의 성능을 시간에 따라 추적할 수 있도록 하며, 데이터 변화, 모델 성능 저하, 또는 사용자 요구의 변화와 같은 문제를 감지하고 대응할 수 있도록 합니다.

**Different types of metrics**

![image.png](RAG-1stWeek\Report_Ragas_metrics\image.png)

### 다양한 형태의 메트릭

메트릭은 사용되는 메커니즘에 따라 두 가지 범주로 분류할 수 있습니다:

- **LLM 기반 메트릭:** 이러한 메트릭은 평가를 수행하기 위해 LLM(대형 언어 모델)을 사용합니다. 점수나 결과를 도출하기 위해 하나 이상의 LLM 호출이 수행될 수 있습니다. 이러한 메트릭은 동일한 입력에 대해 항상 동일한 결과를 반환하지 않을 수 있으므로 다소 비결정적일 수 있습니다. 반면에, 이러한 메트릭은 인간 평가와 더 가까운 정확도를 보이는 것으로 입증되었습니다.
    
    Ragas에서 모든 LLM 기반 메트릭은 MetricWithLLM 클래스에서 상속받습니다. 이러한 메트릭은 점수를 매기기 전에 LLM 객체가 설정되어야 합니다.
    
    ```python
    from ragas.metrics import FactualCorrectness
    scorer = FactualCorrectness(llm=evaluation_llm)
    ```
    
    각 LLM 기반 메트릭은 Prompt 객체를 사용하여 작성된 프롬프트와 연관되어 있습니다.
    
- **Non-LLM 기반 메트릭:** 이러한 메트릭은 평가를 수행하기 위해 LLM을 사용하지 않습니다. 이러한 메트릭은 결정적(deterministic)이며 LLM 없이 AI 애플리케이션의 성능을 평가하는 데 사용할 수 있습니다. 이러한 메트릭은 문자열 유사성, BLEU 점수 등과 같은 전통적인 방법을 통해 AI 애플리케이션의 성능을 평가합니다. 이러한 이유로, 이러한 메트릭은 인간 평가와의 상관관계가 낮은 것으로 알려져 있습니다.
    
    Ragas에서 모든 Non-LLM 기반 메트릭은 Metric 클래스에서 상속받습니다.
    
    메트릭은 평가하는 데이터의 유형에 따라 크게 두 가지 범주로 분류할 수 있습니다:
    
    - Single Turn 메트릭: 이러한 메트릭은 사용자와 AI 간의 단일 상호작용을 기반으로 AI 애플리케이션의 성능을 평가합니다. Ragas에서 단일 회차 평가를 지원하는 모든 메트릭은 SingleTurnMetric 클래스에서 상속받으며, single_turn_ascore 메서드를 사용해 점수가 매겨집니다. 또한 단일 회차 샘플(Single Turn Sample) 객체를 입력으로 기대합니다.
        
        ```python
        from ragas.metrics import FactualCorrectness  # LLM 기반 Single Turn 메트릭 
        
        metric = FactualCorrectness()  # FactualCorrectness 인스턴스 생성
        await metric.single_turn_ascore(sample)  # 단일 회차 샘플에 대해 점수 매기기
        ```
        
    
    - Multi-Turn 메트릭: 이러한 메트릭은 사용자와 AI 간의 다중 상호작용을 기반으로 AI 애플리케이션의 성능을 평가합니다. Ragas에서 다중 회차 평가를 지원하는 모든 메트릭은 MultiTurnMetric 클래스에서 상속받으며, multi_turn_ascore 메서드를 사용해 점수가 매겨집니다. 또한 다중 회차 샘플(Multi Turn Sample) 객체를 입력으로 기대합니다.
        
        ```python
        from ragas.metrics import AgentGoalAccuracy  # LLM 기반 Multi-Turn 메트릭 import
        from ragas import MultiTurnSample  # Multi-Turn 샘플 import
        
        scorer = AgentGoalAccuracy()  # AgentGoalAccuracy 인스턴스 생성
        await metric.multi_turn_ascore(sample)  # 다중 회차 샘플에 대해 점수 매기기
        ```
        

### 메트릭 설계 원칙

AI 애플리케이션에 대한 효과적인 메트릭을 설계하려면 신뢰성, 해석 가능성, 관련성을 보장하기 위해 몇 가지 핵심 원칙을 따라야 합니다. Ragas에서 메트릭을 설계할 때 따르는 다섯 가지 주요 원칙은 다음과 같습니다:

1. 단일 측면 초점(**Single-Aspect Focus**)
    
    하나의 메트릭은 AI 애플리케이션의 성능 중 특정한 한 가지 측면만을 타겟으로 해야 합니다. 이는 메트릭이 해석 가능하고 실질적인 행동을 유도할 수 있도록 하며, 측정되는 항목에 대한 명확한 통찰을 제공합니다.
    
2. 직관적이고 해석 가능한 설계(**Intuitive and Interpretable**)
    
    메트릭은 이해하고 해석하기 쉬워야 합니다. 명확하고 직관적인 메트릭은 결과를 쉽게 전달하고 의미 있는 결론을 도출하는 데 도움을 줍니다.
    
3. 효과적인 프롬프트 흐름(**Effective Prompt Flows**)
    
    대형 언어 모델(LLM)을 사용하여 메트릭을 개발할 때, 인간 평가와 밀접하게 연관된 지능적인 프롬프트 흐름을 사용해야 합니다. 복잡한 작업을 특정 프롬프트를 가진 작은 하위 작업으로 분해하면 메트릭의 정확성과 관련성이 향상됩니다.
    
4. 강건성(**Robustness**)
    
    LLM 기반 메트릭은 원하는 결과를 반영하는 충분한 few-shot 예제를 포함해야 합니다. 이는 메트릭의 강건성을 높이고 LLM이 따를 수 있는 맥락과 지침을 제공하여 더 나은 평가를 보장합니다.
    
5. 일관된 점수 범위(**Consistent Scoring Ranges**)
    
    메트릭 점수 값을 정규화하거나 특정 범위(예: 0에서 1 사이)에 포함되도록 하는 것이 중요합니다. 이렇게 하면 서로 다른 메트릭 간의 비교가 용이하고 평가 프레임워크 전반에서 일관성과 해석 가능성을 유지할 수 있습니다.
    

이러한 원칙은 AI 애플리케이션을 평가할 때 효과적이고 실질적인 메트릭을 만드는 데 기초가 됩니다.

### 사용 가능한 메트릭 목록

Ragas는 LLM 애플리케이션의 성능을 측정하는 데 사용할 수 있는 다양한 평가 메트릭을 제공합니다. 이러한 메트릭은 애플리케이션의 성능을 객관적으로 측정하는 데 도움이 되도록 설계되었습니다. 메트릭은 RAG(Retrieval Augmented Generation) 및 에이전트 워크플로우와 같은 다양한 애플리케이션 및 작업에 사용할 수 있습니다.

각 메트릭은 애플리케이션의 특정 측면을 평가하기 위해 설계된 평가 패러다임입니다. LLM 기반 메트릭은 점수나 결과를 도출하기 위해 하나 이상의 LLM 호출을 사용할 수 있습니다. 사용자 지정 메트릭을 작성하거나 수정할 수도 있습니다.

#### Retrieval Augmented Generation(RAG) 메트릭:

- **Context Precision (문맥 정밀도)**
- **Context Recall (문맥 재현율)**
- **Context Entities Recall (문맥 엔티티 재현율)**
- **Noise Sensitivity (노이즈 민감도)**
- **Response Relevancy (응답 관련성)**
- **Faithfulness (신뢰성)**
- **Multimodal Faithfulness (멀티모달 신뢰성)**
- **Multimodal Relevance (멀티모달 관련성)**

#### 에이전트 또는 도구 사용 사례 메트릭:

- **Topic Adherence (주제 준수)**
- **Tool Call Accuracy (도구 호출 정확성)**
- **Agent Goal Accuracy (에이전트 목표 정확성)**

#### 자연어 비교 메트릭:

- **Factual Correctness (사실 정확성)**
- **Semantic Similarity (의미적 유사성)**
- **Non LLM String Similarity (비 LLM 문자열 유사성)**
- **BLEU Score**
- **ROUGE Score**
- **String Presence (문자열 존재 여부)**
- **Exact Match (정확 일치)**

#### SQL 관련 메트릭:

- **Execution-based Datacompy Score (실행 기반 Datacompy 점수)**
- **SQL Query Equivalence (SQL 쿼리 동등성)**

#### 범용 메트릭:

- **Aspect Critic (측면 비평)**
- **Simple Criteria Scoring (간단한 기준 점수화)**
- **Rubrics-based Scoring (루브릭 기반 점수화)**
- **Instance-specific Rubrics Scoring (인스턴스별 루브릭 점수화)**

#### 기타 작업:

- **Summarization (요약)**

이러한 다양한 메트릭을 활용하여 LLM 애플리케이션의 성능을 다각적으로 평가하고 분석할 수 있습니다.