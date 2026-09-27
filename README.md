# -Construa-seu-Assistente-Virtual-com-Intelig-ncia-Artificial
# Assistente Virtual para Simulação de Sistemas Embarcados e Automação (AutoSim AI)

## O Problema
Estudantes e programadores perdem horas a tentar diagnosticar erros de montagem virtual em simuladores como Wokwi, SimuIDE e CADe_SIMU. Problemas com o mapeamento de pinos do ESP32, lógicas de Ladder para sistemas de envase ou configurações de interrupções no Arduino Uno frequentemente atrasam o desenvolvimento de protótipos de eficiência energética.

## A Solução
O **AutoSim AI** é um assistente virtual desenhado para guiar utilizadores na resolução de problemas de simulação de hardware e automação. O assistente compreende dúvidas sobre ligações de sensores, fornece orientações baseadas numa base de conhecimento específica de microcontroladores e CLPs, e evita respostas inventadas.

## Valor do Projeto
Reduz o tempo de depuração de circuitos em ambientes virtuais, permitindo focar na lógica de programação (C++ ou Ladder) em vez de gastar horas na configuração de portas e sensores de temperatura/umidade.

## Estrutura do Projeto

* `docs/`: Documentação de comportamento e métricas de avaliação
* # Documentação do Assistente (Passo 1)
* **O que faz:** Diagnostica problemas de simulação eletrónica e de automação industrial, focando-se em placas como ESP32 e Arduino, e ambientes como CADe_SIMU e Wokwi.
* **Público-alvo:** Estudantes de engenharia e desenvolvedores de IoT.
* **Comportamento:** Deve adotar um tom técnico, claro e encorajador. Se a dúvida sair do escopo (ex: pedir para escrever um sistema operacional do zero), deve informar que não possui informação suficiente e focar-se apenas na base de dados fornecida.[cite: 8] O assistente também deve ajudar o utilizador a tomar uma próxima decisão estruturada (ex: "Quer verificar o código ou o esquema de ligações?").[cite: 8]

# Avaliação e Métricas (Passo 5)
Para validar se as respostas fazem sentido, o assistente foi testado com base nos seguintes critérios:[cite: 8]
1. **Taxa de Alucinação (Falsos Positivos):** Quantas vezes o assistente inventou um pino inexistente no ESP32. (Alvo: 0%).[cite: 8]
2. **Clareza da Resposta:** A solução para configurar um temporizador em Ladder foi explicada em passos numerados?
3. **Reconhecimento de Limites:** Avaliação da capacidade do bot em dizer "Não tenho essa informação na minha base de conhecimento" ao ser questionado sobre tópicos não relacionados com simulação.[cite: 8]

* `data/`: Ficheiros que compõem a base de conhecimento do assistente.
TÓPICO: ESP32 Super Mini WiFi no Wokwi
- O ESP32 requer tensão de 3.3V. Ligar sensores de 5V diretamente pode danificar as portas lógicas.
- Pinos analógicos recomendados para leitura de sensores (como temperatura): GPIO32 a GPIO39.
- O Wokwi suporta a simulação de ecrãs OLED via protocolo I2C (pinos SDA e SCL padrão).

TÓPICO: Arduino Uno no SimuIDE
- Para projetos de medição de consumo energético, utilizar os pinos A0-A5 para sensores de corrente.
- Se o circuito de reset não funcionar no simulador, verificar se o pino RESET está com um resistor pull-up conectado ao 5V.

TÓPICO: CADe_SIMU e PC_SIMU (Automação Industrial)
- Em simulações de esteiras para sistemas de envase, o sensor ótico e o sensor capacitivo de nível devem ser mapeados nas entradas digitais do CLP (ex: I0.0, I0.1).
- Sempre incluir um intertravamento de emergência na lógica Ladder, cortando a alimentação da bobina principal (Q0.0).
- 
* `src/`: Código fonte da aplicação funcional e os prompts estruturados.
  # app.py
# Simulação simples de uma aplicação funcional para o Assistente Virtual (Passo 4)

def obter_prompt_sistema(base_conhecimento):
    """
    Passo 3: Prompts estruturados para orientar o modelo.
    """
    return f"""Você é o AutoSim AI, um assistente especializado em simuladores de hardware e automação.
    Seu objetivo é entender as dúvidas técnicas dos utilizadores e usar APENAS as informações da seguinte base de conhecimento:
    
    BASE DE CONHECIMENTO:
    {base_conhecimento}
    
    REGRAS DE COMPORTAMENTO:
    1. Responda de forma simples, clara e técnica.
    2. NUNCA invente respostas. Evite respostas inventadas a todo o custo.
    3. Se não tiver informação suficiente na base, diga exatamente: 'Desculpe, não tenho informação suficiente sobre esse tópico para ajudar.'
    4. Termine a resposta ajudando o utilizador a tomar uma próxima decisão (ex: sugerir um teste).
    """

def simular_chat(pergunta_utilizador):
    """
    Função mock que simula a lógica de resposta do LLM baseado no prompt.
    """
    # Carregar base de conhecimento (Passo 2 simulado)
    with open('../data/conhecimento.txt', 'r', encoding='utf-8') as file:
         base_dados = file.read()
         
    prompt_ia = obter_prompt_sistema(base_dados)
    
    # Simulação da interpretação de uma necessidade e resposta (Passo 4)
    print("\n--- O Assistente está a pensar ---")
    if "ESP32" in pergunta_utilizador.upper() and "TENSÃO" in pergunta_utilizador.upper():
        resposta = "O ESP32 requer uma tensão de 3.3V. Ligar sensores de 5V diretamente pode danificar as portas lógicas. Quer que eu reveja as ligações recomendadas para sensores I2C no Wokwi?"
    elif "CADE_SIMU" in pergunta_utilizador.upper() or "LADDER" in pergunta_utilizador.upper():
        resposta = "Para sistemas de envase no CADe_SIMU, garanta que os sensores óticos e capacitivos estão mapeados nas entradas do CLP (ex: I0.0). Lembre-se sempre de incluir um intertravamento de emergência na lógica Ladder. Gostaria de rever a configuração da bobina principal?"
    else:
        # Cumprindo a regra de dizer quando não sabe
        resposta = "Desculpe, não tenho informação suficiente sobre esse tópico para ajudar. A minha especialidade foca-se em ESP32, Arduino Uno, Wokwi, SimuIDE e CADe_SIMU."
        
    return resposta

# Teste Funcional (Interação com o utilizador)
if __name__ == "__main__":
    print("Bem-vindo ao AutoSim AI! Em que configuração de simulação posso ajudar hoje?")
    while True:
        pergunta = input("\nVocê: ")
        if pergunta.lower() in ['sair', 'exit', 'quit']:
            break
            
        resposta_ai = simular_chat(pergunta)
        print(f"AutoSim AI: {resposta_ai}")
  
