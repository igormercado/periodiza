# Especificação de MVP — Sistema de Protocolo de Treinamento

Data da entrevista: 2026-07-21
Prazo: 10 dias, uma pessoa, primeira vez programando.

## 1. O problema

Eu treino musculação cerca de 5 vezes por semana sem acompanhamento de personal
trainer. Isso me deixa na dúvida sobre qual protocolo seguir. Quando busco treinos
prontos na internet, não sei se eles são ideais para o meu corpo e objetivo. Já usei
uma IA genérica (chat) para montar um protocolo, mas:

- Os treinos ficaram repetitivos por muito tempo — parecia "copiar e colar" os
  mesmos exercícios sempre, sem evoluir.
- Não havia um jeito fácil de registrar o que eu realmente fiz no treino.
- Mesmo quando eu descrevia como foi o treino, nada mudava nos treinos seguintes.

## 2. Quem sente esse problema

Só eu, por enquanto. O sistema é de uso pessoal nesta primeira versão. Abrir para
outras pessoas ou comercializar é uma possibilidade para o futuro, **fora do
escopo destes 10 dias**.

## 3. O que eu faço hoje, sem o sistema

Uso um chat de IA genérico para gerar um protocolo pontual. O protocolo não evolui,
não periodiza, e não reage ao que eu relato sobre o treino depois de feito.

## 4. Critério de sucesso (mensurável)

- Consigo abrir o sistema, ver o planejamento da semana e o treino do dia,
  seguindo o protocolo vigente.
- Consigo lançar os dados do **treino inteiro** (6 exercícios, 18 séries: reps,
  carga e observações) em **até 1 minuto**.
- Em até **1 minuto** depois de lançar os dados, o sistema me dá um retorno
  dizendo se vai haver ajuste no protocolo por causa do que registrei.

## 5. Escopo do MVP (o que entra)

### Cadastro inicial (formulário, sem IA)
- Objetivo (ex: hipertrofia, força, emagrecimento).
- Frequência semanal de treino desejada.
- Lesões / restrições de movimento (texto guiado, ex: "não posso agachar livre —
  joelho").
- Áreas do corpo que quero priorizar (eu escolho manualmente, o sistema não
  detecta sozinho).
- Dados da avaliação física: peso, altura, % de gordura, medidas corporais,
  dobras cutâneas, exame de mobilidade — **armazenados para acompanhar minha
  evolução ao longo do tempo**, mas não usados para escolher exercícios
  automaticamente nesta versão.

### Geração do protocolo (regra fixa, sem IA)
- Algoritmo que usa um banco de exercícios marcado por grupo muscular,
  equipamento e contraindicação.
- Segue periodização acadêmica: blocos (mesociclos) de referência de 4 a 6
  semanas, com progressão de carga e trocas de fase / semanas de deload
  seguindo modelos consagrados de educação física.
- Sempre reproduzível: os mesmos dados de entrada geram o mesmo tipo de
  protocolo, sem variação aleatória.

### Uso diário (site web, sem instalar nada)
- Painel com o planejamento da semana.
- Tela do treino do dia, seguindo o protocolo.
- Campos para lançar, por série: repetições, carga e uma observação livre.
- Lançamento do treino inteiro em até 1 minuto, com poucos toques/digitação.
- Retorno imediato (até 1 min) informando se haverá ajuste.

### Ajustes (regra fixa + 1 uso pontual de IA)
- **Ajuste pequeno (a cada treino):** progressão de carga com base em reps e
  carga completadas — regra fixa (ex: progressão dupla).
- **Ajuste maior (a cada bloco, 4–6 semanas):** revisão de fase do treino —
  regra fixa, baseada em periodização acadêmica.
- **Único ponto de IA do sistema:**
  > A IA lê as observações em texto livre que eu escrevo depois de cada treino
  > (dor, cansaço, facilidade, dificuldade) e transforma isso em sinais
  > estruturados, que alimentam as regras fixas de ajuste do protocolo.

## 6. Fora de escopo (explicitamente cortado)

- Aplicativo nativo (loja de app, ícone no celular) — é site web via link.
- Múltiplos usuários, login de terceiros, comercialização.
- Detecção automática de "ponto fraco" do corpo a partir de dobras/circunferências
  para decidir exercícios — quem decide a prioridade sou eu, no formulário.
- IA montando o protocolo do zero — isso é algoritmo fixo, testável.
- Nutrição, integração com wearables/relógios inteligentes, comparação social
  com outros usuários.

## 7. Regra de ouro do projeto

Tudo que é regra clara (periodização, progressão de carga, seleção de
exercícios) é **código comum**, roda sempre igual, é testável. IA entra em
**um único lugar**: interpretar minhas observações de texto livre.
