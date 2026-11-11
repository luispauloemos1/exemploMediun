
import streamlit as st
from google import genai

st.set_page_config(
    page_title="David IA",
    page_icon="👑",
    layout="wide"
)

st.markdown("""
<style>
.stApp {
    background: #09090f;
    color: white;
}
h1 {
    color: #a78bfa;
}
[data-testid="stChatMessage"] {
    border: 1px solid #30234b;
    border-radius: 14px;
}
</style>
""", unsafe_allow_html=True)

st.title("👑 David IA")
st.caption("Sua inteligência artificial pessoal")

if "messages" not in st.session_state:
    st.session_state.messages = []

for msg in st.session_state.messages:
    with st.chat_message(msg["role"]):
        st.markdown(msg["content"])

prompt = st.chat_input("Converse com David IA...")

if prompt:
    st.session_state.messages.append({
        "role": "user",
        "content": prompt
    })

    with st.chat_message("user"):
        st.markdown(prompt)

    try:
        api_key = st.secrets["GEMINI_API_KEY"]
        client = genai.Client(api_key=api_key)

        history = "\n".join(
            f"{m['role']}: {m['content']}"
            for m in st.session_state.messages
        )

        with st.chat_message("assistant"):
            with st.spinner("David IA está pensando..."):
                response = client.models.generate_content(
                    model="gemini-3.8-flash",
                    contents=(
                        "Você é David IA, um assistente "
                        "inteligente, educado e prestativo. "
                        "Responda em português.\n\n"
                        + history
                    )
                )

                answer = response.text or "Não consegui gerar uma resposta."
                st.markdown(answer)

        st.session_state.messages.append({
            "role": "assistant",
            "content": answer
        })

    except Exception:
        st.error(
            "Não consegui conectar à IA. "
            "Verifique a configuração da chave e tente novamente."
        )
        
