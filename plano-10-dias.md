# Plano de 10 dias — Sistema de Protocolo de Treinamento

Baseado em `especificacao-mvp.md`. Este é um esboço inicial — vamos ajustar
conforme a gente for construindo e descobrindo o que demora mais ou menos do
que o previsto.

Lógica da ordem: primeiro construímos o "motor" (o algoritmo que gera e ajusta
o protocolo), porque é a parte mais arriscada e mais valiosa do sistema. Telas
bonitas por cima de um motor que não existe não resolvem seu problema. Só
depois que o motor funciona é que construímos as telas.

## Dia 1 — Feito hoje
Entrevista de escopo. Especificação do MVP escrita e validada com você.

## Dia 2 — Fundação técnica
- Eu escolho e monto o "esqueleto" do projeto (linguagem, banco de dados,
  hospedagem) — decisão técnica minha, você não precisa entender os detalhes,
  só aprovar a explicação em português simples.
- Desenho o banco de dados: usuário, avaliação física, objetivo/restrições,
  banco de exercícios, protocolo, blocos (mesociclos), treinos, séries
  registradas.
- Você aprova o desenho antes de eu seguir.

## Dia 3 — Banco de exercícios + regra de periodização
- Monto a base de exercícios (nome, grupo muscular, equipamento,
  contraindicações).
- Escrevo a regra fixa de periodização (blocos de 4–6 semanas, fases,
  deload), baseada em literatura de educação física.
- Sem tela ainda — eu testo isso "por trás", te mostrando o resultado em
  texto/tabela.

## Dia 4 — Algoritmo de geração do protocolo
- Junto o banco de exercícios + a regra de periodização + seus dados de
  cadastro (objetivo, frequência, restrições, áreas prioritárias) num
  algoritmo que gera o protocolo completo.
- Testamos com dados fictícios seus (e reais, se você já quiser cadastrar os
  de verdade) até o protocolo fazer sentido pra você.

## Dia 5 — Regra de progressão de carga
- Escrevo a regra fixa de ajuste "pequeno": se você completou as reps com a
  carga, sugere subir peso; se não completou, mantém ou ajusta pra baixo.
- Ainda sem tela — validamos com cenários (“e se eu completar tudo 3 treinos
  seguidos?”, “e se eu falhar?”).

## Dia 6 — Cadastro e formulário de objetivo (primeira tela)
- Construo a tela de cadastro: avaliação física, objetivo, frequência,
  restrições, áreas prioritárias.
- Você usa de verdade, com seus dados reais.

## Dia 7 — Painel da semana + treino do dia
- Tela com o planejamento semanal.
- Tela do treino do dia, mostrando exercícios/séries/reps/carga planejados,
  seguindo o protocolo gerado.

## Dia 8 — Lançamento de treino (o coração do sucesso mensurável)
- Construo a tela de registro rápido: reps, carga e observação por série,
  otimizada para poucos toques.
- Testamos o cronômetro de verdade: você lança um treino de 6 exercícios / 18
  séries e medimos se fica abaixo de 1 minuto. Ajustamos a interface até bater
  a meta.

## Dia 9 — IA nas observações + motor de ajuste conectado
- Conecto a IA só no lugar combinado: ler suas observações de texto livre e
  transformar em sinal (dor, fácil, difícil, cansaço).
- Esse sinal + os dados numéricos alimentam as regras fixas dos dias 3 e 5.
- Testamos o ciclo completo: registrar treino → ver o retorno em até 1 minuto
  → conferir se o próximo treino realmente mudou quando deveria.

## Dia 10 — Ajustes finais e validação
- Correção de bugs encontrados nos testes reais.
- Deixo o sistema publicado num link acessível pelo seu celular.
- Revalidamos juntos os dois critérios de sucesso definidos na especificação:
  lançamento em até 1 min, retorno de ajuste em até 1 min.
- Lista do que ficou de fora e vira "próxima versão" (ex: detecção automática
  de ponto fraco, multiusuário).

## Observação importante
Este cronograma é uma aposta inicial, não uma promessa rígida. Se algum dia
render menos que o esperado (comum em quem está aprendendo a programar
junto), a válvula de escape é sempre cortar escopo de novo — nunca esticar o
prazo de 10 dias às custas da meta mensurável do Dia 8.
