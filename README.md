# fotomelvi
import streamlit as st
from datetime import datetime, time, timedelta
from pathlib import Path
import os

st.set_page_config(page_title="Foto Melvi", page_icon="📸", layout="centered")

# ---- CONFIG ----
UPLOAD_ROOT = Path("uploads")
if not UPLOAD_ROOT.exists():
    UPLOAD_ROOT.mkdir(parents=True)

# ---- SIDEBAR MENU ----
menu = st.sidebar.radio(
    "Ir para…",
    ("Página Inicial", "Sobre nós", "Serviços", "Preçário", "Encomendas", "Contacte-nos", "Área de Funcionário")
)

# ---- PAGES ----
if menu == "Página Inicial":
 with tab_main:
    st.image("https://images.unsplash.com/photo-1464983953574-0892a716854b?auto=format&fit=facearea&w=1200&h=500&q=80", use_column_width=True)
    st.markdown("""
    # Foto Melvi
    **Figueiró dos Vinhos**  
    *A fotografia é a arte de congelar o tempo e transformar algo comum em extraordinário.*
    """)

    st.subheader("O Casulo do Malhoa")
    st.image("https://upload.wikimedia.org/wikipedia/commons/2/2a/Casulo_Malhoa_FV.jpg", caption="Casulo do Malhoa, Figueiró dos Vinhos", use_column_width=True)

elif menu == "Sobre nós":
    st.header("Sobre nós")
    st.markdown("""
    Bem-vindo à nossa família! Somos uma empresa familiar com mais de 20 anos de serviço.  
    Já celebrámos inúmeros batizados e casamentos e fazemos parte da história de vida de muitos figueiroenses.  
    Estamos localizados em Figueiró dos Vinhos, mas prestamos serviços em todo o país.  
    Vamos criar memórias juntos.
    """)
    st.subheader("Horário")
    st.write("Dias úteis: 9h-13h e 14h-18h")

elif menu == "Serviços":
    st.header('Serviços disponíveis')
    st.markdown("""
    - Fotografar Casamento
    - Fotografar Batizado
    - Álbum Digital
    - Impressão de fotos
    """)

elif menu == "Preçário":
    st.header("Preçário")
    st.markdown("## Impressão de fotografias")
    st.write("""
| Tamanho      | 1-50 fotos | 51-100 | 101-200 | >200   |
|--------------|-----------|--------|---------|--------|
| 10x15        | 0,50 €    | 0,45 € | 0,40 €  | 0,35 € |
| 15x20        | 0,60 €    | 0,55 € | 0,50 €  | 0,45 € |
| 20x30        | 0,70 €    | 0,65 € | 0,60 €  | 0,55 € |
| Personalizado| Pedir orçamento |   |   |   |
    """)
    st.markdown("## Impressão em Telas")
    st.write("""
| Tamanho      | Preço    |
|--------------|----------|
| 10x15        | 20,00 €  |
| 15x20        | 30,00 €  |
| 20x30        | 40,00 €  |
| Personalizado| Pedir orçamento |
    """)
    st.markdown("## Cerimónias")
    st.write("""
|              | Simples | Premium |
|--------------|---------|---------|
| Casamento    | 600 €   | 900 €   |
| Batizado     | 450 €   | 650 €   |
    """)
    st.markdown("## Outros")
    st.write("""
|                | Em Estúdio | Fora do estúdio |
|----------------|------------|-----------------|
| Sessão Fotográfica | 50 €   | 70 €            |
| Outros serviços    | Pedir orçamento |         |
    """)
    st.info("A consulta de preços não dispensa a solicitação de um orçamento que se ajuste ao seu caso. Aos valores apresentados acresce IVA conforme legislação em vigor.")

elif menu == "Encomendas":
    st.header("Nova Encomenda de Fotografias")
    op_entrega = st.selectbox("Opção:", ["Click & go (1h disponíveis)", "Regime normal (3-5 dias úteis)"])
    tamanho = st.selectbox("Tamanho das fotos", ["10x15", "15x20", "20x30", "tipo-passe", "personalizado"])
    n_fotos = st.selectbox("Número de fotos a imprimir", ["Até 50 fotos", "Até 100 fotos", "Até 200 fotos", "Acima de 200 fotos"])
    uploaded_files = st.file_uploader("Carregue as fotos (JPEG)", accept_multiple_files=True, type=['jpg','jpeg'])
    telemovel = st.text_input("Número de telemóvel")
    email = st.text_input("Email")
    submitted = st.button("Submeter Encomenda")
    if submitted:
        if not uploaded_files:
            st.warning("Por favor, carregue pelo menos uma foto.")
        elif not email or not telemovel:
            st.warning("Por favor, preencha todos os campos de contacto.")
        else:
            pasta_dest = UPLOAD_ROOT / f"encomenda_{datetime.now().strftime('%Y%m%d_%H%M%S')}"
            pasta_dest.mkdir()
            for uf in uploaded_files:
                with open(pasta_dest / uf.name, "wb") as out_file:
                    out_file.write(uf.getbuffer())
            with open(pasta_dest / "info.txt", "w") as f:
                f.write(f"Tamanho: {tamanho}\nEntrega: {op_entrega}\nTelemóvel: {telemovel}\nEmail: {email}\nNº fotos: {len(uploaded_files)}\n")
            st.success(f"Encomenda recebida! {len(uploaded_files)} fotos submetidas. Será contactadx em breve. Guardado na pasta '{pasta_dest}'.")

elif menu == "Contacte-nos":
    st.header("Contacte-nos")
    tabs = st.tabs(["Agendar reunião", "Agendar sessão fotográfica", "Formulário Geral"])
    with tabs[0]:
        st.subheader("Agendar reunião")
        email = st.text_input("Email (reunião)", key="email_reuniao")
        assunto = st.text_input("Assunto", key="assunto_reuniao")
        dt = st.date_input("Escolha a data", min_value=datetime.today())
        hora = st.time_input("Hora (dias úteis, 9h-18h)", value=time(9,0))
        if st.button("Agendar", key="agendar_reuniao"):
            st.success("Pedido de agendamento registado! Será contactadx por e-mail.")
    with tabs[1]:
        st.subheader("Agendar sessão fotográfica")
        email = st.text_input("Email (sessão)", key="email_sessao")
        finalidade = st.selectbox("Finalidade da sessão", ["Natal", "Família", "Revelação", "Namoro", "Pedido de Casamento", "Outro"], key="finalidade")
        local = st.selectbox("Local", ["Estúdio", "Outro"], key="local_sessao")
        dt = st.date_input("Data", min_value=datetime.today(), key="data_sessao")
        hora = st.time_input("Hora (dias úteis, 9h-18h)", value=time(9,0), key="hora_sessao")
        if st.button("Agendar", key="agendar_sessao"):
            st.success("Sessão registada! Será contactadx por e-mail.")
    with tabs[2]:
        st.subheader("Formulário Geral")
        nome = st.text_input("Nome", key="nome_form")
        email = st.text_input("Email", key="email_form")
        assunto = st.selectbox("Assunto", ["Pedir orçamento", "Estado da Encomenda", "Outro"], key="assunto_form")
        mensagem = st.text_area("Mensagem", key="msg_form")
        files = st.file_uploader("Anexar fotos (opcional)", type=['jpg','jpeg'], accept_multiple_files=True, key="anexos_form")
        if st.button("Enviar", key="enviar_form"):
            st.success("Formulário enviado! Será contactadx por e-mail.")

elif menu == "Área de Funcionário":
    st.header("Área de Funcionário - Download dos uploads")
    pwd = st.text_input("Password de funcionário", type="password")
    if pwd == "melvi2024":
        st.success("Acesso permitido!")
        # Listar encomendas
        encomendas = [p for p in UPLOAD_ROOT.iterdir() if p.is_dir()]
        for enc in sorted(encomendas, reverse=True):
            st.subheader(f"{enc.name}")
            files = list(enc.glob('*'))
            for f in files:
                if f.suffix in [".jpg", ".jpeg"]:
                    st.image(str(f), width=210)
                    with open(f, "rb") as fp:
                        st.download_button("Download imagem", fp, file_name=f.name)
                elif f.suffix == ".txt":
                    st.download_button("Info.txt", open(f, "rb"), file_name="info.txt")
    elif pwd:
        st.error("Password inválida.")
