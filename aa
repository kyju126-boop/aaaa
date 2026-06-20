import streamlit as st
import re

# 페이지 기본 설정
st.set_page_config(page_title="국어 서·논술형 자동 채점 시스템", layout="wide")

st.title("📝 2회 고사 대비 국어 서·논술형 자동 채점기")
st.caption("작성된 채점 로직 및 루브릭 기준을 바탕으로 학생들의 답안을 정밀 분석합니다.")

# -------------------------------------------------------------------------
# 채점 핵심 로직 함수 정의
# -------------------------------------------------------------------------

def check_keywords(text, keywords):
    """텍스트 내에 필수 키워드가 하나라도 포함되어 있는지 검사 (유연한 허용 범위 반영)"""
    return any(kw in text.replace(" ", "") for kw in keywords)

def extract_method(text):
    """괄호 안의 설명 방법 명칭 추출 (예: (예시), (비교와 대조))"""
    match = re.search(r'\((.*?)\)', text)
    return match.group(1).strip() if match else None

# --- 1세트 채점 로직 ---
def grade_set1(inputs):
    score = 0
    feedback = {}
    
    # 문항 1-(1)
    if check_keywords(inputs['q1_1'], ["쉬운과제", "취미", "큰노력", "난이도가낮은"]):
        score += 2
        feedback['q1_1'] = "✅ 정답 (핵심 의미 인정)"
    else:
        feedback['q1_1'] = "❌ 오답 (과제의 난이도 특성 누락)"
        
    # 문항 1-(2)
    if check_keywords(inputs['q1_2'], ["혼자", "독립"]) and check_keywords(inputs['q1_2'], ["집중", "차분"]):
        score += 2
        feedback['q1_2'] = "✅ 정답 (환경적 특성 기술 완료)"
    else:
        feedback['q1_2'] = "❌ 오답 ('혼자'와 '집중' 키워드 매칭 실패)"
        
    # 문항 1-(3)
    if inputs['q1_3'].replace(" ", "") in ["사회적억제", "사회적억제현상"]:
        score += 2
        feedback['q1_3'] = "✅ 정답 (학술 용어 일치)"
    else:
        feedback['q1_3'] = "❌ 오답 (정확한 명칭 미기재)"

    # 문항 2 (설명문 작성)
    q2_texts = [inputs['q2_1'], inputs['q2_2']]
    q2_scores = [0, 0]
    q2_feedbacks = ["", ""]
    used_methods = []
    
    for i, text in enumerate(q2_texts):
        method = extract_method(text)
        if not method:
            q2_feedbacks[i] = "❌ 감점 (문장 끝에 설명 방법 명칭 괄호 표기 누락)"
            continue
        
        used_methods.append(method)
        clean_text = text.replace(" ", "")
        
        # 오개념 검증: 쉬운 과제 유형에 '혼자'를 쓰거나 어려운 과제 유형에 '모임'을 섞어 쓰면 감점
        if "쉬운" in text and "혼자" in text:
            q2_feedbacks[i] = f"❌ 오답 ({method} 양식은 맞으나, '쉬운 과제=혼자'라는 오개념 발견)"
            continue
            
        # 선택지별 로직 검증
        if "예시" in method:
            if check_keywords(text, ["예를들어", "예시", "공부모임", "도서관"]):
                q2_scores[i] = 2
                q2_feedbacks[i] = "✅ 정답 (예시 기법 정상 구사)"
            else:
                q2_feedbacks[i] = "❌ 오답 (예시를 드러내는 연결어 및 사례 부족)"
        elif "비교" in method or "대조" in method:
            if check_keywords(text, ["반면", "달리", "차이", "반대로"]):
                q2_scores[i] = 2
                q2_feedbacks[i] = "✅ 정답 (대조 접속어 및 두 대상의 차이점 명시 완료)"
            else:
                q2_feedbacks[i] = "❌ 오답 (비교·대조의 문장 성분 불충분)"
        else:
            q2_feedbacks[i] = f"🔺 확인 요망 (정의된 루브릭 외 설명 방법 '{method}' 적용됨 - 수동 채점 필요)"
            
    if len(set(used_methods)) < len(used_methods) and len(used_methods) == 2:
        q2_feedbacks[1] += " ⚠️ 감점 (서로 다른 2가지 방법 사용 조건 위배)"
        q2_scores[1] = 0
        
    score += sum(q2_scores)
    feedback['q2_1'] = q2_feedbacks[0]
    feedback['q2_2'] = q2_feedbacks[1]

    # 문항 3 (영상 연출)
    # 시각
    if check_keywords(inputs['q3_v'], ["혼자", "방", "스탠드", "독립"]) and check_keywords(inputs['q3_v_e'], ["자극차단", "집중", "전달", "이해"]):
        score += 2
        feedback['q3_v'] = "✅ 정답 (시각 연출 및 지문 연계 효과 서술 적합)"
    else:
        feedback['q3_v'] = "❌ 오답 (시각 연출 설정 또는 주제 전달 효과 미흡)"
    # 청각
    if check_keywords(inputs['q3_a'], ["무음", "고요", "초침", "소음최소"]) and check_keywords(inputs['q3_a_e'], ["대비", "고요", "분위기", "몰두"]):
        score += 2
        feedback['q3_a'] = "✅ 정답 (청각 연출 및 대비 효과 서술 적합)"
    else:
        feedback['q3_a'] = "❌ 오답 (청각 연출 설정 또는 분위기 조성 효과 미흡)"
        
    return score, feedback

# --- 2세트 채점 로직 ---
def grade_set2(inputs):
    score = 0
    feedback = {}
    
    if check_keywords(inputs['q1_1'], ["고여있는물", "고여있는"]): score += 2; feedback['q1_1'] = "✅ 정답"
    else: feedback['q1_1'] = "❌ 오답 (비유어 매칭 실패)"
        
    if check_keywords(inputs['q1_2'], ["이동하지", "머물러", "정지"]): score += 2; feedback['q1_2'] = "✅ 정답"
    else: feedback['q1_2'] = "❌ 오답 (전하의 정지 상태 누락)"
        
    if check_keywords(inputs['q1_3'], ["위험하지않음", "피해가없음", "안전"]): score += 2; feedback['q1_3'] = "✅ 정답"
    else: feedback['q1_3'] = "❌ 오답 (위험성 판단 오류)"

    # 문항 2
    for i in ['1', '2']:
        text = inputs[f'q2_{i}']
        method = extract_method(text)
        if not method:
            feedback[f'q2_{i}'] = "❌ 감점 (방법 명칭 괄호 표기 누락)"
            continue
        
        if "흐름" in text and "정전기" in text:
            feedback[f'q2_{i}'] = "❌ 오답 (정전기를 흐르는 전하로 기술한 오개념 발견)"
            continue

        if "정의" in method:
            if check_keywords(text, ["전하가정지", "말한다", "뜻한다", "란"]):
                score += 2; feedback[f'q2_{i}'] = "✅ 정답 (정의 양식 통과)"
            else: feedback[f'f2_{i}'] = "❌ 오답 (개념의 뜻을 규명하지 않음)"
        elif "비교" in method or "대조" in method:
            if check_keywords(text, ["실생활", "반면", "달리", "차이"]):
                score += 2; feedback[f'q2_{i}'] = "✅ 정답 (대조 양식 통과)"
            else: feedback[f'q2_{i}'] = "❌ 오답 (실생활 전기와의 대조 부족)"
            
    # 문항 3
    if check_keywords(inputs['q3_v'], ["고여", "멈춘", "댐", "호수"]) and "대조" in inputs['q3_v_e']:
        score += 2; feedback['q3_v'] = "✅ 정답"
    else: feedback['q3_v'] = "❌ 오답 (정적 시각 자료 연결 미흡)"
        
    if check_keywords(inputs['q3_a'], ["무음", "고요", "소리없"]) and check_keywords(inputs['q3_a_e'], ["안도", "위험하지"]):
        score += 2; feedback['q3_a'] = "✅ 정답"
    else: feedback['q3_a'] = "❌ 오답 (청각을 통한 안전성 유도 기술 미흡)"
        
    return score, feedback

# --- 3세트 채점 로직 ---
def grade_set3(inputs):
    score = 0
    feedback = {}
    
    if check_keywords(inputs['q1_1'], ["다른감정", "다르게", "개인차"]): score += 2; feedback['q1_1'] = "✅ 정답"
    else: feedback['q1_1'] = "❌ 오답 (개인적 차이 서술 부족)"
        
    if check_keywords(inputs['q1_2'], ["천천히음미", "아름다움"]): score += 2; feedback['q1_2'] = "✅ 정답"
    else: feedback['q1_2'] = "❌ 오답 (지표 행동 '천천히 음미' 누락)"
        
    if check_keywords(inputs['q1_3'], ["맛을제대로", "느낄수없"]): score += 2; feedback['q1_3'] = "✅ 정답"
    else: feedback['q1_3'] = "❌ 오답 (결과론적 매칭 실패)"

    # 문항 2
    for i in ['1', '2']:
        text = inputs[f'q2_{i}']
        method = extract_method(text)
        if not method:
            feedback[f'q2_{i}'] = "❌ 감점 (방법 명칭 괄호 표기 누락)"
            continue
            
        if "빠르게" in text and "올바른" in text:
            feedback[f'q2_{i}'] = "❌ 오답 ('빠르게 달리는 삶 = 올바른 태도'라는 논리 방향 오류)"
            continue

        if "비교" in method or "대조" in method:
            if check_keywords(text, ["음식", "삶", "인생", "듯이"]):
                score += 2; feedback[f'q2_{i}'] = "✅ 정답"
            else: feedback[f'q2_{i}'] = "❌ 오답 (유추적 대조 성분 부족)"
        elif "인과" in method:
            if check_keywords(text, ["때문에", "결과", "해서는", "없다"]):
                score += 2; feedback[f'q2_{i}'] = "✅ 정답"
            else: feedback[f'q2_{i}'] = "❌ 오답 (인과적 귀결 문장 미비)"
            
    # 문항 3
    if check_keywords(inputs['q3_v'], ["여유", "풍경", "느린", "슬로"]) and "대조" in inputs['q3_v_e']:
        score += 2; feedback['q3_v'] = "✅ 정답"
    else: feedback['q3_v'] = "❌ 오답 (슬로우 비디오 연출 맥락 누락)"
        
    if check_keywords(inputs['q3_a'], ["편안", "자연", "바람", "새소리"]) and "공감" in inputs['q3_a_e']:
        score += 2; feedback['q3_a'] = "✅ 정답"
    else: feedback['q3_a'] = "❌ 오답 (정서적 청각 분위기 매칭 실패)"
        
    return score, feedback

# -------------------------------------------------------------------------
# Streamlit 탭 구성 및 UI UI 레이아웃
# -------------------------------------------------------------------------

tab1, tab2, tab3 = st.tabs(["🎯 1세트 (사회적 촉진/억제)", "⚡ 2세트 (정전기 특징)", "🍏 3세트 (음식과 삶)"])

# --- 1세트 UI ---
with tab1:
    st.header("실전 적용-1 문항 답안 입력")
    col1, col2 = st.columns(2)
    
    with col1:
        st.subheader("[서·논술형 1] 표 빈칸 채우기")
        q1_1 = st.text_input("빈칸 (1) 입력", key="s1_q1_1", placeholder="예: 비교적 쉬운 취미 생활")
        q1_2 = st.text_input("빈칸 (2) 입력", key="s1_q1_2", placeholder="예: 차분하게 혼자 집중하는 시간을 가짐")
        q1_3 = st.text_input("빈칸 (3) 입력", key="s1_q1_3", placeholder="예: 사회적 억제")
        
        st.subheader("[서·논술형 2] 이어지는 설명문 완성")
        st.caption("주어진 문장: 과제의 특성과 난이도에 따라 우리의 학습 효율을 높이는 방법은 다르게 적용되어야 한다.")
        q2_1 = st.text_area("첫 번째 문장 작성 (설명 방법 포함)", key="s1_q2_1", placeholder="예: 예를 들어... (예시)")
        q2_2 = st.text_area("두 번째 문장 작성 (설명 방법 포함)", key="s1_q2_2", placeholder="예: 쉬운 과제는 반면... (비교와 대조)")

    with col2:
        st.subheader("[서·논술형 3] 영상 연출 계획안")
        q3_v = st.text_input("장면 2 시각 요소 (Ⓐ)", key="s1_q3_v")
        q3_v_e = st.text_area("시각 요소 (Ⓐ)의 효과", key="s1_q3_v_e")
        q3_a = st.text_input("장면 2 청각 요소 (Ⓑ)", key="s1_q3_a")
        q3_a_e = st.text_area("청각 요소 (Ⓑ)의 효과", key="s1_q3_a_e")

    if st.button("1세트 답안 채점하기", type="primary"):
        set1_inputs = {'q1_1': q1_1, 'q1_2': q1_2, 'q1_3': q1_3, 'q2_1': q2_1, 'q2_2': q2_2, 'q3_v': q3_v, 'q3_v_e': q3_v_e, 'q3_a': q3_a, 'q3_a_e': q3_a_e}
        final_score, res = grade_set1(set1_inputs)
        
        st.metric(label="총점", value=f"{final_score} / 14 점")
        st.json(res)

# --- 2세트 UI ---
with tab2:
    st.header("실전 적용-2 문항 답안 입력")
    col1, col2 = st.columns(2)
    
    with col1:
        st.subheader("[서·논술형 1] 표 빈칸")
        s2_q1_1 = st.text_input("빈칸 (1) 입력", key="s2_q1_1")
        s2_q1_2 = st.text_input("빈칸 (2) 입력", key="s2_q1_2")
        s2_q1_3 = st.text_input("빈칸 (3) 입력", key="s2_q1_3")
        
        st.subheader("[서·논술형 2] 설명문")
        s2_q2_1 = st.text_area("첫 번째 문장", key="s2_q2_1", placeholder="예: 정전기란... (정의)")
        s2_q2_2 = st.text_area("두 번째 문장", key="s2_q2_2", placeholder="예: 실생활 전기와 달리... (비교와 대조)")

    with col2:
        st.subheader("[서·논술형 3] 영상 계획안")
        s2_q3_v = st.text_input("시각 요소 (Ⓐ)", key="s2_q3_v")
        s2_q3_v_e = st.text_area("시각 요소 효과", key="s2_q3_v_e")
        s2_q3_a = st.text_input("청각 요소 (Ⓑ)", key="s2_q3_a")
        s2_q3_a_e = st.text_area("청각 요소 효과", key="s2_q3_a_e")

    if st.button("2세트 답안 채점하기", type="primary"):
        set2_inputs = {'q1_1': s2_q1_1, 'q1_2': s2_q1_2, 'q1_3': s2_q1_3, 'q2_1': s2_q2_1, 'q2_2': s2_q2_2, 'q3_v': s2_q3_v, 'q3_v_e': s2_q3_v_e, 'q3_a': s2_q3_a, 'q3_a_e': s2_q3_a_e}
        final_score, res = grade_set2(set2_inputs)
        st.metric(label="총점", value=f"{final_score} / 14 점")
        st.json(res)

# --- 3세트 UI ---
with tab3:
    st.header("실전 적용-3 문항 답안 입력")
    col1, col2 = st.columns(2)
    
    with col1:
        st.subheader("[서·논술형 1] 표 빈칸")
        s3_q1_1 = st.text_input("빈칸 (1) 입력", key="s3_q1_1")
        s3_q1_2 = st.text_input("빈칸 (2) 입력", key="s3_q1_2")
        s3_q1_3 = st.text_input("빈칸 (3) 입력", key="s3_q1_3")
        
        st.subheader("[서·논술형 2] 설명문")
        s3_q2_1 = st.text_area("첫 번째 문장", key="s3_q2_1", placeholder="예: 음식을 먹을 때처럼... (비교와 대조)")
        s3_q2_2 = st.text_area("두 번째 문장", key="s3_q2_2", placeholder="예: 빠르게 달리기만 하면... (인과)")

    with col2:
        st.subheader("[서·논술형 3] 영상 계획안")
        s3_q3_v = st.text_input("시각 요소 (Ⓐ)", key="s3_q3_v")
        s3_q3_v_e = st.text_area("시각 요소 효과", key="s3_q3_v_e")
        s3_q3_a = st.text_input("청각 요소 (Ⓑ)", key="s3_q3_a")
        s3_q3_a_e = st.text_area("청각 요소 효과", key="s3_q3_a_e")

    if st.button("3세트 답안 채점하기", type="primary"):
        set3_inputs = {'q1_1': s3_q1_1, 'q1_2': s3_q1_2, 'q1_3': s3_q1_3, 'q2_1': s3_q2_1, 'q2_2': s3_q2_2, 'q3_v': s3_q3_v, 'q3_v_e': s3_q3_v_e, 'q3_a': s3_q3_a, 'q3_a_e': s3_q3_a_e}
        final_score, res = grade_set3(set3_inputs)
        st.metric(label="총점", value=f"{final_score} / 14 점")
        st.json(res)
