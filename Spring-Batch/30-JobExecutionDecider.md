# JobExecutionDecider

- ExitStatus 를 조작하거나 StepExecutionListener 를 등록할 필요없이 **Transition 처리를 위한 전용 클래스**
    - 여러 Step 들을 많은 조건에 따른 흐름에 구성하다보면 가독성이 떨어질 수 있음
        - on, to, end 와 같은 메서드가 복잡하게 구성이되면, 강한 결합이 생김
    - ➡️ **JobExecutionDecider 안에서 자체적으로 조건에 따라 흐름을 정의할 수 있음**
- Step 과 Transition 역할을 명확히 분리해서 설정할 수 있음
- Step 의 ExitStatus 가 아닌 JobExecutionDecider 의 FlowExecutionStatus 상태값을 새롭게 설정해서 반환함
    - JobFlow 같은 경우 Step 의 ExitStatus 값이 FlowExecutionStatus 값으로 반영된 다음, 다시 JobFlow 값으로 반영이됨
        - ➡️ **Step 의 ExitStatus 값에 따라 FlowExecutionStatus 값이 정해짐**