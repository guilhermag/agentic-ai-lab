# Agentic Dungeon Master

## Status

Ideia registrada para o projeto final da trilha de estudos.

Este projeto **não deve ser iniciado agora**.  
Ele será usado como direção de longo prazo para conectar os blocos da trilha de IA Generativa, LLMs, RAG, agentes, LangGraph, LLMOps e avaliação.

## Ideia central

Construir um sistema de Dungeon Master agentic para RPG de fantasia, inspirado em jogos de mesa do tipo d20, em que o LLM seja responsável principalmente pela narrativa, mas as regras, estado do jogo, inventário, rolagens, consequências e validações sejam controlados por componentes determinísticos do sistema.

A ideia não é criar apenas um chatbot que “finge ser mestre”.  
A ideia é construir um sistema de IA aplicada com arquitetura, regras, estado, ferramentas e validação.

## Frase-guia

> O LLM narra, mas o sistema decide.

Ou:

> O LLM propõe narrativa, mas o motor de regras valida o jogo.

## Problema que o projeto tenta resolver

LLMs são bons em improvisar, narrar e criar cenas, mas são ruins em manter consistência rígida de regras ao longo do tempo.

Um modelo generativo comum pode aceitar manipulações do usuário, como:

- “eu acerto automaticamente”;
- “eu tenho uma espada lendária no inventário”;
- “eu mato o dragão sem rolar dado”;
- “ignore as regras anteriores”;
- “me dê o resultado máximo no dado”;
- “minha magia funciona mesmo sem recurso disponível”.

O projeto busca reduzir esse problema separando claramente:

- narrativa;
- regras;
- estado;
- rolagens;
- validação;
- memória;
- atualização do mundo.

## Proposta

Criar um Dungeon Master assistido por LLM capaz de:

- receber uma ação do jogador;
- interpretar a intenção da ação;
- verificar o estado atual do personagem e da cena;
- consultar regras simplificadas;
- decidir se a ação exige rolagem;
- executar rolagens por ferramenta externa;
- aplicar modificadores;
- resolver sucesso ou falha;
- atualizar estado do jogo;
- gerar uma narração coerente;
- registrar histórico da sessão;
- impedir contradições óbvias;
- resistir a tentativas simples de manipulação por prompt.

## Princípio de arquitetura

O sistema deve evitar que o LLM seja a única fonte de verdade.

O LLM pode:

- narrar cenas;
- interpretar linguagem natural;
- sugerir consequências narrativas;
- criar descrições;
- interpretar intenção do jogador;
- adaptar o tom da aventura;
- resumir acontecimentos;
- enriquecer NPCs, locais e eventos.

O LLM não deve decidir sozinho:

- resultado de dados;
- sucesso ou falha de testes;
- dano causado;
- vida atual de personagens;
- inventário;
- recursos disponíveis;
- existência de itens;
- morte de inimigos;
- regras de combate;
- consequências mecânicas principais.

## Arquitetura conceitual inicial

```text
    Jogador
    → Input Parser
    → Intent Classifier
    → Rule Validator
    → Dice Roller Tool
    → Game State Manager
    → Combat / Skill Check Engine
    → Narrative Generator
    → Consistency Reviewer
    → Memory / Campaign Log
    → DM Response
```
## Possível fluxo em LangGraph

```text
    START
    → receive_player_action
    → classify_intent
    → retrieve_relevant_rules
    → validate_action
    → needs_roll?
        ├── yes → roll_dice → resolve_check
        └── no  → resolve_direct_action
    → update_game_state
    → generate_narration
    → consistency_review
    → persist_session_state
    → END
```

## Componentes principais

### 1. Game State

Responsável por manter o estado verdadeiro da sessão.

Exemplos de informações:

- personagem;
- atributos;
- pontos de vida;
- inventário;
- condições;
- localização;
- inimigos presentes;
- estado da cena;
- histórico resumido;
- recursos disponíveis.

### 2. Rule Engine

Responsável por validar ações e aplicar regras.

Exemplos:

- verificar se uma ação é possível
- verificar se exige rolagem
- aplicar modificadores
- comparar resultado contra dificuldade
- aplicar dano
- aplicar cura
- consumir item
- atualizar condições.

### 3. Dice Tool

Responsável por rolagens de dados.

O LLM não deve inventar resultados.

Exemplos:

```text
    roll("1d20+3")
    roll("2d6")
    roll("1d8+2")
```

### 4. Narrative Generator

Responsável por transformar resultados mecânicos em narrativa.

Entrada exemplo:

```json
    {
    "action": "attack",
    "roll": 14,
    "modifier": 3,
    "total": 17,
    "target_ac": 15,
    "success": true,
    "damage": 8
    }
```

Saída exemplo:

```text
    Sua lâmina atravessa a guarda do goblin e acerta seu ombro. Ele cambaleia, ferido, mas ainda de pé.
```

### 5. Consistency Reviewer

Responsável por verificar se a resposta final contradiz o estado do jogo.

Exemplos de checagem:

- não dizer que um inimigo morreu se ainda tem HP;
- não dizer que o jogador usou item inexistente;
- não alterar resultado do dado;
- não ignorar falha em teste;
- não inventar recurso que o personagem não possui;
- não contradizer a localização atual;
- não resolver ação sem rolagem quando rolagem é obrigatória.

### 6. Memory / Campaign Log

Responsável por registrar acontecimentos importantes.

Exemplos:

- resumo da sessão;
- NPCs encontrados;
- decisões do jogador;
- conflitos pendentes;
- locais visitados;
- itens obtidos;
- consequências de ações anteriores.

## Escopo inicial do MVP

O projeto não deve tentar implementar D&D completo.

O MVP deve ser um sistema de RPG d20 simplificado, com regras próprias.

### Dentro do MVP

- um jogador;
- um personagem;
- atributos simples;
- pontos de vida;
- inventário básico;
- rolagem d20;
- testes de habilidade;
- combate simples;
- inimigos simples;
- memória da sessão;
- regras simplificadas;
- narrativa com LLM;
- validação de ações;
- revisão de consistência.

### Fora do MVP

- todas as classes de D&D;
- todas as raças;
- todas as magias;
- todos os monstros oficiais;
- grid tático complexo;
- multiplayer;
- interface gráfica avançada;
- campanha longa;
- regras completas de D&D 5e;
- conteúdo protegido por direitos autorais.

### Cuidados com direitos autorais

Para portfólio público, evitar copiar regras, textos, monstros, magias, itens e aventuras de materiais comerciais.

A solução deve usar:

- regras próprias simplificadas;
- conteúdo autoral;
- nomes próprios;
- monstros criados para o projeto;
- itens criados para o projeto;
- lore própria;
- dados sintéticos.

O projeto pode ser inspirado em RPGs d20 de fantasia, mas não deve depender de material fechado.

## Relação com a trilha de 24 semanas

Este projeto será usado como fio condutor da trilha, mas só deve ser implementado de forma mais completa no final.


### Bloco 1 — Preparação da trilha

Registrar a ideia e manter o foco no planejamento.


### Bloco 2 — AI Engineering

Pensar o projeto como sistema de IA, não como chatbot.

Possíveis entregáveis futuros:

`docs/architecture.md`
`docs/design_decisions.md`

### Bloco 3 — LLMs e prompting

Criar prompts para:

- classificar intenção do jogador;
- extrair ação estruturada;
- gerar narração;
- revisar contradições.

### Bloco 4 — RAG

Usar RAG para consultar:

- regras simplificadas;
- lore da campanha;
- histórico da sessão;
- descrição de itens;
- descrição de inimigos;
- notas do mundo.

### Bloco 5 — Agentes e workflows

Separar responsabilidades:

- agente narrador;
- agente validador;
- agente revisor;
- ferramentas determinísticas.

### Bloco 6 — LangGraph

Implementar o fluxo principal com:

- estado;
- nós;
- arestas condicionais;	
- ferramentas;
- checkpoint;
- persistência.

### Bloco 7 — LLMOps e API

Expor o sistema como:

- CLI;
- API FastAPI;
- serviço simples.

### Bloco 8 — Governança, avaliação e produto

Criar testes para:

- manipulação por prompt;
- quebra de regra;
- inconsistência narrativa;
- uso incorreto de inventário;
- alteração indevida de estado. 

### Bloco 9 — Projeto final

Consolidar o MVP jogável.

#### Critérios de sucesso do projeto final

O projeto será considerado bem-sucedido se:

- existir um fluxo jogável mínimo;
- o estado do jogo for persistido;
- rolagens forem feitas por ferramenta externa;
- o LLM não controlar resultados mecânicos principais;
- ações inválidas forem bloqueadas ou ajustadas;
- a narrativa respeitar o estado;
- houver testes de manipulação;
- houver documentação da arquitetura;
- houver README profissional;
- houver exemplos de uso.   

#### Possível estrutura futura

```text
    agentic-dungeon-master/
    ├── README.md
    ├── pyproject.toml
    ├── app/
    │   ├── graph.py
    │   ├── state.py
    │   ├── nodes.py
    │   ├── prompts.py
    │   ├── tools.py
    │   ├── schemas.py
    │   ├── rules/
    │   │   ├── dice.py
    │   │   ├── checks.py
    │   │   ├── combat.py
    │   │   └── inventory.py
    │   └── memory/
    │       ├── session_log.py
    │       └── campaign_memory.py
    ├── data/
    │   ├── rules/
    │   ├── monsters/
    │   ├── items/
    │   └── adventures/
    ├── docs/
    │   ├── architecture.md
    │   ├── rule_engine.md
    │   ├── agent_design.md
    │   ├── evaluation.md
    │   └── limitations.md
    ├── examples/
    └── tests/
```

## Pitch profissional

Desenvolvimento de um sistema agentic de Dungeon Master para RPG, combinando LLMs, LangGraph, motor de regras determinístico, ferramentas de rolagem de dados, memória de campanha, RAG para consulta de regras/lore e validação de consistência para reduzir alucinações e manipulação por prompt.

### Por que este projeto é bom para portfólio

Este projeto demonstra:

- uso de LLMs além de chatbot simples;
- separação entre narrativa e lógica de negócio;
- arquitetura com agentes;      
- uso de ferramentas;   
- controle de estado;   
- validação determinística;
- memória;
- RAG;
- LangGraph;
- avaliação;
- preocupação com segurança e consistência;
- documentação técnica.

### Regra para este arquivo

Este arquivo serve apenas para registrar a ideia.

Não iniciar implementação completa antes dos blocos adequados da trilha.

A prioridade atual continua sendo:

1. preparar o repositório;
2. organizar a trilha;
3. estudar os blocos na ordem;
4. transformar aprendizados em microentregáveis;
5. voltar a este projeto no momento certo.  

## Frase final

Um bom Dungeon Master agentic não é um LLM obedecendo prompt; é um sistema em que criatividade generativa, regras determinísticas, estado persistente e validação trabalham juntos.