# SBS 환경성적 분석

이 프로젝트는 SBS(Sustainable Buying System)에서 제품의 환경성적을 평가하여, 탄소발자국, 물발자국, 자원발자국을 기준으로 산업별로 주요 지표의 우선순위를 파악하는 것을 목표로 합니다. 분석을 통해 각 산업군에 맞춘 친환경 지표를 활용하여 친환경 제품을 소비자에게 제안합니다.

## 설치 방법

1. 리포지토리를 클론합니다:
    ```bash
    git clone https://github.com/username/SBS-analysis.git
    cd SBS-analysis
    ```

2. 필요한 라이브러리를 설치합니다:
    ```bash
    pip install -r requirements.txt
    ```

3. 노트북을 실행하여 분석을 수행합니다:
    ```bash
    jupyter notebook SBS.ipynb
    ```

## 데이터 설명

이 프로젝트는 다음과 같은 환경성적 데이터를 분석합니다:
- **탄소발자국**: 대기로 방출된 이산화탄소 등 온실가스 물질이 지구의 기후변화에 미치는 영향
- **물발자국**: 농업, 공업 등 인간 활동이 수질, 수량 등 수자원에 미치는 영향
- **자원발자국**: 대기 중으로 배출된 프레온가스 등 오존층 파괴 물질이 성층권에 존재하는 오존층에 미치는 영향

## 분석 방법
1. **이상치 처리**: Z-Score와 Isolation Forest 기법을 적용하여 각 발자국의 이상치를 탐지하고, 분석 결과에 왜곡이 발생하지 않도록 필터링합니다.
    - ![image](https://github.com/user-attachments/assets/133e24a2-6117-4f8f-a37b-29b8d7d28e47)
2. **결측치 처리**: 프로토타입에서는 물 데이터만 모아서 분석했습니다. 물 데이터에선 결측치가 발견되지 않아 생략했습니다. 실제 서비스에서는 평균과 KNN을 이용해 처리할 계획입니다.
3. **데이터 누락 처리**: 누락된 데이터는 비슷한 제품을 기준으로 평균과 GAN을 사용해 대체합니다. 대체 값은 (평균값+GAN 생성값)의 평균입니다.
    ``` python
    # Calculate the combined values (mean + generated data) / 2 for each product
    combined_values = (mean_values + generated_df.drop(columns=['인증제품명'])).div(2)
    
    # Update the generated DataFrame with the combined values
    missing_products_df = generated_df.copy()
    missing_products_df[mean_values.index] = combined_values.values
    
    # Concatenate this with the original water_data
    final_dataset = pd.concat([water_data, missing_products_df], ignore_index=True)
    ```
    ![image](https://github.com/user-attachments/assets/914134eb-4c5b-4682-b667-73fafc5bd8c7)
4. **PCA를 통한 주요 지표 분석**: 각 산업별로 PCA를 적용하여 가장 큰 변동성을 설명하는 발자국 지표를 주요 지표로 선정합니다. 이를 통해 각 산업군별로 가장 중요한 환경성적 지표를 도출합니다.
    ![image](https://github.com/user-attachments/assets/25645372-09a1-4831-ad34-525f088ef4e3)
5. **데이터 분석 및 기타 처리**: 물 데이터는 같은 제품(ex 삼다수)일 때, 용량이 클 수록 개당 배출량이 커지고, L당 배출량은 작아지는 추세를 확인헀습니다. 따라서 같은 제품이면, 용량에 관계없이 같은 배출량을 갖도록 데이터를 처리 했습니다.
    ![image](https://github.com/user-attachments/assets/f0cf0f3b-2d3f-4931-b299-e34401cd7c6c)
## 결과 요약

- **PCA 분석 결과**: 각 산업군별로 주요 변동성 지표를 파악했습니다. 물 상품에서는 탄소발자국이 가장 주요 지표로 여겨졌습니다.
- **데이터 처리 결과**: 누락 및 이상치를 보완하여 데이터의 신뢰도를 높였습니다.
- **그래프**: 아래는 '환경 순위 기반 정렬'로 물 데이터 순위를 보여주는 막대그래프 입니다.
- ![image](https://github.com/user-attachments/assets/cf7eb332-838d-40c3-8316-5440993f68f9)

## 참고 사항

- PCA를 통한 지표 분석은 데이터의 분산을 기준으로 하므로, 실제 산업의 중요 지표와는 차이가 있을 수 있습니다. 이를 보완하기 위해 도메인 전문가와 협업하여 가중치를 조정할 수 있습니다.
- 동일 제품의 용량 차이에 따라 발자국이 다를 수 있으므로, 모든 제품에 대해 단위당 발자국을 계산하여 일관된 비교를 수행했습니다.
- 추후에 이 외에도 원재료명, 포장재질, 영양정보 등을 활용하여 산업군별로 분석에 필요한 추가적인 정보도 포함하여, 데이터 생성을 더 정교하게 할 예정입니다.


---
