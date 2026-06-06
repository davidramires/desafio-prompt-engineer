# Desafio Prompt Engineer - Bug to User Story

Otimização de prompts usando LangChain e LangSmith para converter relatórios de bugs em User Stories de alta qualidade.

## Objetivo

Criar um software capaz de:
- Fazer pull de prompts do LangSmith Prompt Hub
- Refatorar e otimizar usando técnicas avançadas de Prompt Engineering
- Fazer push dos prompts otimizados de volta ao LangSmith
- Avaliar a qualidade através de métricas customizadas
- Atingir pontuação mínima de 0.9 (90%) em todas as métricas

## Pré-requisitos
- Python 3.9+
- Conta no LangSmith
- API Key da OpenAI ou Google (Gemini)

## Instalação

```bash
# Criar ambiente virtual
python3 -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Instalar dependências
pip install -r requirements.txt
```

## Configuração

Crie um arquivo `.env` com suas credenciais:

```env
LANGCHAIN_API_KEY=your_langsmith_api_key
LANGCHAIN_TRACING_V2=true
LANGCHAIN_PROJECT=desafio-prompt-engineer

# Escolha um provider
OPENAI_API_KEY=your_openai_api_key
# ou
GOOGLE_API_KEY=your_google_api_key
```

## Como Executar

### 1. Configurar ambiente
```bash
python3 -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
```

### 2. Configurar credenciais
```bash
cp .env.example .env
# Edite .env com suas API keys
```

### 3. Pull dos prompts iniciais
```bash
python src/pull_prompts.py
```

### 4. Refatorar prompts
Edite o arquivo `prompts/bug_to_user_story_v2.yml` aplicando as técnicas de prompt engineering.

### 5. Validar estrutura do prompt
```bash
pytest tests/test_prompts.py
```

### 6. Push dos prompts otimizados
```bash
python src/push_prompts.py
```

### 7. Avaliação
```bash
python src/evaluate.py
```

> Repita os passos 4 a 7 até todas as métricas atingirem >= 0.9 (esperado: 3-5 iterações).

### Comandos Rápidos

| Ação | Comando |
|------|---------|
| Instalar dependências | `pip install -r requirements.txt` |
| Pull prompt inicial | `python src/pull_prompts.py` |
| Validar estrutura | `pytest tests/test_prompts.py` |
| Push prompt otimizado | `python src/push_prompts.py` |
| Avaliar qualidade | `python src/evaluate.py` |
| Executar todos os testes | `pytest tests/` |

## Estrutura do Projeto

```
desafio-prompt-engineer/
├── .env.example              # Template das variáveis de ambiente
├── requirements.txt          # Dependências Python
├── README.md                 # Documentação
│
├── prompts/
│   ├── bug_to_user_story_v1.yml       # Prompt inicial (após pull)
│   └── bug_to_user_story_v2.yml       # Prompt otimizado
│
├── src/
│   ├── pull_prompts.py       # Pull do LangSmith
│   ├── push_prompts.py       # Push ao LangSmith
│   ├── evaluate.py           # Avaliação automática
│   ├── metrics.py            # 4 métricas implementadas
│   ├── dataset.py            # 15 exemplos de bugs
│   └── utils.py              # Funções auxiliares
│
└── tests/
    └── test_prompts.py       # Testes de validação
```

## Métricas de Avaliação

Todas as métricas devem atingir >= 0.9:
- **Tone Score**: Avalia o tom e linguagem
- **Acceptance Criteria Score**: Avalia critérios de aceitação
- **User Story Format Score**: Avalia o formato da User Story
- **Completeness Score**: Avalia a completude

## Técnicas Aplicadas (Fase 2)

Seis técnicas avançadas de Prompt Engineering foram aplicadas para otimizar a conversão de bug reports em User Stories:

### 1. Role Prompting *(obrigatória)*

- **O que é**: Atribuição de uma persona específica ao LLM para guiar seu comportamento e tom.
- **Por que escolhi**: Um Product Manager Sênior empático é o profissional ideal para essa tarefa — entende tanto o lado técnico do bug quanto a necessidade do usuário, produzindo User Stories centradas em valor real.
- **Como apliquei**: `"Você é um Product Manager Sênior empático que transforma bugs em User Stories de alta qualidade. Você coloca o usuário no centro de tudo e escreve com empatia genuína."`

### 2. Few-Shot Learning *(obrigatória)*

- **O que é**: Fornecimento de exemplos concretos de entrada/saída para demonstrar o comportamento esperado.
- **Por que escolhi**: Exemplos eliminam ambiguidade — ao ver um bug transformado em User Story empática e bem estruturada, o modelo calibra exatamente o tom, formato e nível de detalhe esperados.
- **Como apliquei**: Dois exemplos completos no YAML (`examples`): bug de login mobile e bug de pagamento com Amex, ambos com User Story, critérios Dado-Quando-Então e seções de contexto técnico.

### 3. Chain of Thought (CoT)

- **O que é**: Instrução para o modelo raciocinar passo a passo antes de produzir a resposta final.
- **Por que escolhi**: A transformação de bug em User Story exige análise: identificar o usuário afetado, entender a frustração, articular o valor da solução. Sem CoT o modelo pula direto para a escrita sem contexto adequado.
- **Como apliquei**: Sequência de 6 passos no prompt: (1) Identificar usuário, (2) Sentir a frustração, (3) Transformar em desejo positivo, (4) Articular valor real, (5) Extrair detalhes técnicos, (6) Definir critérios verificáveis.

### 4. Negative Examples (Contrastive Learning)

- **O que é**: Mostrar explicitamente o que NÃO fazer, contrastando exemplos ruins com exemplos bons.
- **Por que escolhi**: O maior problema do prompt original era o tom frio e técnico. Mostrar o contraste (`❌ ERRADO vs ✅ CORRETO`) é mais eficaz do que apenas descrever o que se quer.
- **Como apliquei**: Seção de tom com exemplos contrastivos: `"Como um usuário, eu quero que o botão funcione"` (ruim) vs `"Como um cliente que depende do app..."` (bom).

### 5. Rubric-based Prompting

- **O que é**: Incluir os critérios de avaliação diretamente no prompt, para que o modelo saiba exatamente como será julgado.
- **Por que escolhi**: Se o modelo conhece a rubrica, ele pode otimizar sua resposta para cada critério. É especialmente útil quando há múltiplas dimensões de qualidade.
- **Como apliquei**: Seção `"COMO VOCÊ SERÁ AVALIADO"` com os 4 critérios e seus subcritérios (0-10): Tom, Critérios de Aceitação, Formato e Completude.

### 6. Emotional Priming

- **O que é**: Frases que ativam um estado emocional no modelo antes da geração, influenciando o tom da resposta.
- **Por que escolhi**: O Tone Score era a métrica mais difícil de elevar. Ativar empatia explicitamente antes da escrita produz linguagem mais humana e centrada no usuário.
- **Como apliquei**: Instruções no início do prompt: `"🎯 VOCÊ É A VOZ DO USUÁRIO. Antes de escrever qualquer coisa, imagine a pessoa real por trás do bug report. Ela tinha algo importante para fazer e foi impedida. Sinta sua frustração."` e `"USE LINGUAGEM POSITIVA"`.

### Melhorias Estruturais

- **Formato de Saída Padronizado**: Template Markdown com 4 seções obrigatórias (User Story, Critérios de Aceitação, Contexto Técnico, Impacto e Prioridade)
- **Critérios de Aceitação**: Formato Dado-Quando-Então com 6-8 critérios testáveis
- **Preservação de Dados**: Instruções explícitas para manter IDs, valores e erros do bug original
- **Linguagem Positiva**: Foco no que o usuário QUER fazer, não no que está quebrado

## Resultados Finais

### Status

> ⏳ Preencher após executar `python src/evaluate.py` e atingir todas as métricas >= 0.9

### Link do LangSmith Dashboard

O prompt otimizado está disponível publicamente no LangSmith Hub:
- **Prompt**: `https://smith.langchain.com/prompts/bug_to_user_story_v2` *(atualizar com seu username)*
- **Projeto**: `https://smith.langchain.com/projects/desafio-prompt-engineer` *(atualizar com seu username)*

### Scores Finais

> ⏳ Preencher após aprovação (todas as métricas >= 0.9)

| Métrica | Score | Status |
|---------|-------|--------|
| Tone Score | - | ⏳ |
| Acceptance Criteria Score | - | ⏳ |
| User Story Format Score | - | ⏳ |
| Completeness Score | - | ⏳ |
| **Média** | - | ⏳ |

### Screenshots das Avaliações

> ⏳ Adicionar screenshots do dashboard do LangSmith mostrando as notas >= 0.9

### Tabela Comparativa: Prompt v1 (original) vs v2 (otimizado)

| Aspecto | v1 (original) | v2 (otimizado) |
|---|---|---|
| **Persona/Role** | Nenhuma | Product Manager Sênior empático |
| **Exemplos (Few-shot)** | Nenhum | 2 exemplos completos |
| **Tom** | Técnico e frio | Empático e centrado no usuário |
| **Critérios de Aceitação** | Ausentes | 6-8 critérios Dado-Quando-Então |
| **Contexto Técnico** | Não preservado | Seção dedicada com todos os detalhes |
| **Formato de saída** | Livre | Template Markdown com 4 seções fixas |
| **Técnicas aplicadas** | 0 | 6 técnicas avançadas |

### Configuração Final

- **Provider**: Google Gemini (gratuito)
- **Modelo de Geração**: gemini-2.5-flash
- **Modelo de Avaliação**: gemini-2.5-flash

### Nota sobre Custos

Este projeto utiliza **Google Gemini**, que possui plano gratuito mais que suficiente para o desafio:

| Limite do Plano Gratuito | Valor |
|---|---|
| Requisições por minuto | 15 |
| Requisições por dia | 1.500 |

O volume total de chamadas é de aproximadamente **75 por iteração** (15 bugs × 4 métricas + 15 gerações). Em 13 iterações, são ~975 chamadas — bem dentro do limite diário gratuito.

| Modelo | Custo (se pago) |
|---|---|
| gemini-2.5-flash (geração e avaliação) | $0,075 / 1M tokens |

> **Custo total estimado: $0** (plano gratuito cobre todo o desafio).
> No pior caso, se ultrapassar o free tier: menos de **$0,10**.


## Licença

MIT
