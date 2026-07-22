# MODELO.md — As tabelas do Periodiza

Este documento é a planta do banco de dados: toda tabela, todo campo e o tipo de
cada campo, antes de existir uma linha de código. Não é código — é o desenho que
o mockup de amanhã e o banco de depois vão seguir.

## Como ler este documento

- **Tabela**: uma "gaveta" onde guardamos um tipo de dado (ex: a gaveta dos
  treinos registrados). Cada linha dentro da gaveta é um registro (ex: um
  treino específico que você fez na terça-feira).
- **Campo**: uma informação dentro da linha (ex: "peso", "data").
- **Tipo**: que tipo de valor o campo aceita.
  - **Texto**: qualquer palavra ou frase digitada.
  - **Número**: um valor numérico (inteiro ou com casa decimal).
  - **Data**: um dia do calendário (e às vezes hora também).
  - **Lista fechada**: o campo só pode receber um valor de uma lista pré-definida
    por nós — nunca texto livre digitado à mão. Isso existe para o código poder
    confiar no valor (ex: comparar `status = 'concluido'` funciona sempre; comparar
    texto livre não funciona, porque cada um escreve diferente).
  - **Referência**: o campo guarda o identificador de uma linha de outra tabela,
    criando um link entre elas (ex: uma série "aponta" para o treino a que
    pertence). É assim que modelamos relações "um para muitos".
- **id**: todo tabela tem um campo `id`, o identificador único daquela linha.
  Não repito ele campo a campo abaixo, mas ele existe em todas.

## Regra que não se negoça: `user_id` em toda tabela de dado pessoal

Toda tabela que guarda algo que você fez ou informou (seu cadastro, sua
avaliação física, seu treino registrado) tem um campo `user_id`, mesmo o login
ainda não existindo hoje.

> **Por quê o dono nasce junto com o dado, e não depois:** se a gente guardar o
> dado sem dono agora e tentar "adicionar o dono depois", vamos ter que decidir
> retroativamente de quem é cada linha antiga — um trabalho arriscado e sujeito a
> erro. Nascendo com `user_id` desde a primeira linha, no dia em que o login
> existir (Supabase, conforme `ARQUITETURA.md`) já sabemos exatamente de quem é
> cada dado, sem migração nenhuma.

**Exceção:** a tabela `exercicio` (o catálogo de exercícios) não é um dado seu —
é uma lista de referência que o algoritmo consulta (como um dicionário), igual
para qualquer pessoa que um dia use o sistema. Por isso ela não tem `user_id`.
Todas as outras tabelas abaixo têm.

---

## Grupo 1 — Cadastro inicial

### `usuario`
A pessoa dona dos dados. Hoje existe só uma linha aqui (você), mas a tabela já
nasce pronta para o login do Supabase.

| Campo | Tipo | Observação |
|---|---|---|
| nome | Texto | |
| email | Texto | |
| criado_em | Data | |

### `cadastro_inicial`
As respostas do formulário inicial (seção 5 do ESPEC.md).

| Campo | Tipo | Observação |
|---|---|---|
| user_id | Referência | Dono do dado → `usuario` |
| objetivo | Lista fechada | `hipertrofia`, `forca`, `emagrecimento` |
| frequencia_semanal_desejada | Número | Quantas vezes por semana quer treinar |
| lesoes_restricoes | Texto | Texto guiado, ex: "não posso agachar livre — joelho" |
| criado_em | Data | |

### `area_priorizada`
Um cadastro pode ter **várias** áreas do corpo priorizadas — por isso é uma
tabela separada, ligada ao cadastro por referência (um para muitos), e não um
campo único de texto.

| Campo | Tipo | Observação |
|---|---|---|
| user_id | Referência | Dono do dado → `usuario` |
| cadastro_inicial_id | Referência | A qual cadastro pertence → `cadastro_inicial` |
| area_corporal | Lista fechada | `peito`, `costas`, `ombro`, `braco`, `perna`, `gluteo`, `abdomen` |

---

## Grupo 2 — Avaliação física (seu histórico de evolução)

### `avaliacao_fisica`
Uma "foto" da sua condição física em uma data. Você vai ter várias ao longo do
tempo — é esse histórico que alimenta o **Painel de Evolução** (o infográfico
combinado hoje), sem precisar de nenhuma tabela extra: o painel lê o histórico
aqui e em `serie_registrada` e calcula as diferenças na hora.

| Campo | Tipo | Observação |
|---|---|---|
| user_id | Referência | Dono do dado → `usuario` |
| data_avaliacao | Data | |
| peso_kg | Número | |
| altura_cm | Número | |
| percentual_gordura | Número | |
| exame_mobilidade | Texto | Anotação guiada sobre mobilidade |
| criado_em | Data | |

### `medida_corporal`
Uma avaliação tem **várias** medidas de circunferência (braço, cintura, coxa
etc.) — tabela separada, um para muitos.

| Campo | Tipo | Observação |
|---|---|---|
| user_id | Referência | Dono do dado → `usuario` |
| avaliacao_fisica_id | Referência | A qual avaliação pertence → `avaliacao_fisica` |
| local_medida | Lista fechada | `braco`, `cintura`, `quadril`, `coxa`, `panturrilha`, `torax` |
| valor_cm | Número | |

### `dobra_cutanea`
Uma avaliação também tem **várias** dobras cutâneas medidas em pontos
diferentes do corpo — mesma lógica, tabela separada.

| Campo | Tipo | Observação |
|---|---|---|
| user_id | Referência | Dono do dado → `usuario` |
| avaliacao_fisica_id | Referência | A qual avaliação pertence → `avaliacao_fisica` |
| local_dobra | Lista fechada | `triciptal`, `subescapular`, `suprailiaca`, `abdominal`, `coxa` |
| valor_mm | Número | |

---

## Grupo 3 — Catálogo de exercícios (referência, sem dono)

### `exercicio`
O banco de exercícios que o algoritmo usa para montar o protocolo (seção 5.2
do ESPEC.md). Não tem `user_id` — veja a exceção explicada acima.

| Campo | Tipo | Observação |
|---|---|---|
| nome | Texto | |
| grupo_muscular | Lista fechada | `peito`, `costas`, `ombro`, `biceps`, `triceps`, `quadriceps`, `posterior_coxa`, `gluteo`, `panturrilha`, `abdomen` |
| equipamento | Lista fechada | `barra`, `halteres`, `maquina`, `peso_corporal`, `cabo`, `kettlebell` |

### `exercicio_contraindicacao`
Um exercício pode ter **várias** contraindicações (ex: um agachamento livre
contraindica joelho E lombar) — tabela separada.

| Campo | Tipo | Observação |
|---|---|---|
| exercicio_id | Referência | A qual exercício pertence → `exercicio` |
| restricao | Lista fechada | `joelho`, `ombro`, `lombar`, `punho`, `quadril`, `tornozelo` |

---

## Grupo 4 — Protocolo e periodização

### `protocolo`
O protocolo gerado pelo algoritmo a partir do seu cadastro (seção 5.2).

| Campo | Tipo | Observação |
|---|---|---|
| user_id | Referência | Dono do dado → `usuario` |
| cadastro_inicial_id | Referência | De qual cadastro este protocolo nasceu → `cadastro_inicial` |
| data_geracao | Data | |
| status | Lista fechada | `ativo`, `encerrado` |

### `mesociclo`
Um bloco de periodização de 4 a 6 semanas dentro do protocolo (seção 5.2).

| Campo | Tipo | Observação |
|---|---|---|
| user_id | Referência | Dono do dado → `usuario` |
| protocolo_id | Referência | A qual protocolo pertence → `protocolo` |
| numero_ordem | Número | Posição do bloco dentro do protocolo (1º, 2º...) |
| fase | Lista fechada | `adaptacao`, `hipertrofia`, `forca`, `deload` — *lista provisória, para revisarmos juntos quando formos detalhar o algoritmo de periodização* |
| data_inicio | Data | |
| data_fim | Data | |

### `treino_planejado`
O que o protocolo prescreve para um dia da semana dentro de um mesociclo.

| Campo | Tipo | Observação |
|---|---|---|
| user_id | Referência | Dono do dado → `usuario` |
| mesociclo_id | Referência | A qual mesociclo pertence → `mesociclo` |
| dia_semana | Lista fechada | `segunda`, `terca`, `quarta`, `quinta`, `sexta`, `sabado`, `domingo` |
| nome_treino | Texto | Ex: "Treino A — Peito/Tríceps" |

### `exercicio_planejado`
Um treino planejado tem **vários** exercícios — tabela separada, um para
muitos, ligada também ao catálogo (`exercicio`).

| Campo | Tipo | Observação |
|---|---|---|
| user_id | Referência | Dono do dado → `usuario` |
| treino_planejado_id | Referência | A qual treino planejado pertence → `treino_planejado` |
| exercicio_id | Referência | Qual exercício do catálogo → `exercicio` |
| ordem | Número | Ordem de execução dentro do treino |
| series_alvo | Número | Quantas séries planejadas |
| reps_alvo_min | Número | |
| reps_alvo_max | Número | |
| carga_sugerida_kg | Número | Sugestão inicial, ajustada depois pela regra de progressão |

---

## Grupo 5 — Caderno de Treinos (o que você realmente fez)

Este é o grupo com a feature de IA que você definiu ontem: você cola o texto
cru da sua observação pós-treino, e a IA extrai sinais estruturados (dor,
cansaço, facilidade, dificuldade) para você conferir antes de salvar.

### `treino_registrado`
Uma sessão de treino que você realmente fez — o "pedido" virou "treino
registrado" na nossa conversa. Tem os campos que a IA vai preencher no futuro
(`texto_bruto` e `confianca`), para o banco não precisar ser remodelado quando
a IA chegar no dia 9.

| Campo | Tipo | Observação |
|---|---|---|
| user_id | Referência | Dono do dado → `usuario` |
| treino_planejado_id | Referência | A qual treino planejado corresponde → `treino_planejado` |
| data_treino | Data | |
| status | Lista fechada | `em_andamento`, `concluido` |
| texto_bruto | Texto | O texto cru que você cola sobre como foi o treino (dor, cansaço, facilidade, dificuldade) |
| confianca | Número | O quanto a IA confia no que entendeu do `texto_bruto` (0 a 1, por exemplo) |

> **Nota sobre "conferir antes de salvar":** o `texto_bruto` e a sugestão da IA
> aparecem na tela para você revisar. Só quando você confirma é que os sinais
> extraídos (tabela `sinal_extraido`, abaixo) são de fato gravados no banco —
> por isso não precisamos de um status "rascunho" aqui: o que existe na tela
> antes da confirmação simplesmente ainda não virou uma linha salva.

### `serie_registrada`
Um treino registrado tem **várias** séries — a mesma relação "um para muitos"
que você pediu para o antigo "pedido com vários itens". Aqui é onde entram os
campos do dia a dia (seção 5.3 do ESPEC.md: reps, carga e observação por
série).

| Campo | Tipo | Observação |
|---|---|---|
| user_id | Referência | Dono do dado → `usuario` |
| treino_registrado_id | Referência | A qual treino registrado pertence → `treino_registrado` |
| exercicio_id | Referência | Qual exercício foi feito → `exercicio` |
| numero_serie | Número | 1ª, 2ª, 3ª série daquele exercício |
| repeticoes | Número | |
| carga_kg | Número | Esse histórico, por exercício, é a fonte da "evolução de carga por exercício" no Painel de Evolução |
| observacao_livre | Texto | Observação rápida daquela série específica |

### `sinal_extraido`
O resultado da IA depois que você confirma: um treino registrado pode gerar
**vários** sinais (ex: "dor" no joelho durante o agachamento E "facilidade" no
supino) — por isso é tabela separada, um para muitos, com referência opcional
ao exercício específico.

| Campo | Tipo | Observação |
|---|---|---|
| user_id | Referência | Dono do dado → `usuario` |
| treino_registrado_id | Referência | A qual treino registrado pertence → `treino_registrado` |
| exercicio_id | Referência | Opcional — a qual exercício o sinal se refere, se for específico (pode ficar vazio se for sobre o treino todo) |
| tipo_sinal | Lista fechada | `dor`, `cansaco`, `facilidade`, `dificuldade` |
| intensidade | Lista fechada | `baixa`, `media`, `alta` |

---

## Resumo das relações "um para muitos"

```
cadastro_inicial  ──< area_priorizada
avaliacao_fisica  ──< medida_corporal
avaliacao_fisica  ──< dobra_cutanea
exercicio         ──< exercicio_contraindicacao
protocolo         ──< mesociclo
mesociclo         ──< treino_planejado
treino_planejado  ──< exercicio_planejado
treino_registrado ──< serie_registrada
treino_registrado ──< sinal_extraido
```
`A ──< B` se lê "A tem vários B".

---

## Pontos que deixei marcados para sua decisão

1. A lista de `fase` em `mesociclo` (`adaptacao`, `hipertrofia`, `forca`,
   `deload`) é um rascunho meu, baseado no que o ESPEC.md descreve em termos
   gerais — quando formos desenhar o algoritmo de periodização, vamos revisar
   os nomes e a ordem certa com mais cuidado.
2. `intensidade` em `sinal_extraido` (`baixa`, `media`, `alta`) também é um
   rascunho meu para a IA ter algo estruturado para preencher — podemos ajustar
   quando virmos exemplos reais de texto colado.

Está de acordo com esse modelo? Posso seguir para o `ARQUITETURA.md`, ou quer
ajustar alguma tabela antes?

