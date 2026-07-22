# MAPA-DE-TELAS.md — O que é público e o que é protegido

Este documento mostra quais telas qualquer pessoa pode abrir e quais só abrem
para quem está logado, e o caminho que a informação percorre entre a tela, o
banco e (a partir do dia 9) a IA.

## PÚBLICO × PROTEGIDO

| PÚBLICO (qualquer um abre) | PROTEGIDO (só quem está logado) |
|---|---|
| Página de apresentação do Periodiza | Cadastro inicial (objetivo, frequência, lesões, áreas priorizadas) |
| Login / criar conta | Avaliação física (peso, altura, %gordura, medidas, dobras, mobilidade) |
| | Painel da semana (planejamento vigente) |
| | Treino do dia (lançar reps, carga e observação por série) |
| | Caderno de Treinos (histórico dos treinos registrados; a partir do dia 9, colar o texto cru para a IA) |
| | Painel de Evolução (infográfico: peso, gordura, medidas, força, carga por exercício ao longo do tempo) |

**Por que essa divisão:** tudo que é seu dado pessoal (seu corpo, seu treino,
sua evolução) exige login — é exatamente o que a coluna `user_id` em cada
tabela do `MODELO.md` protege. Só a apresentação e a porta de entrada (login)
ficam abertas para qualquer visitante, porque ainda não há dado nenhum de
ninguém envolvido nelas.

## O caminho, em diagrama

```
                    ┌─────────────────────────────┐
                    │   Seu celular / navegador    │
                    │  (tela pública ou protegida)  │
                    └───────────────┬───────────────┘
                                    │
              ┌─────────────────────┼─────────────────────┐
              │                                             │
   lê/grava dados do MODELO.md                 (a partir do dia 9)
   e confere login                              manda o texto_bruto
              │                                             │
              v                                             v
   ┌────────────────────┐                       ┌────────────────────┐
   │      SUPABASE       │                       │   EDGE FUNCTION     │
   │  banco de dados +    │                       │  (roda no servidor, │
   │  login               │                       │   não no celular)   │
   └────────────────────┘                       └──────────┬───────────┘
                                                              │
                                                   chave da IA trancada
                                                   só aqui, nunca sai
                                                              │
                                                              v
                                                   ┌────────────────────┐
                                                   │   Serviço de IA     │
                                                   └────────────────────┘
```

O navegador nunca guarda nem vê a chave da IA — ela fica trancada dentro da
Edge Function, conforme registrado no `ARQUITETURA.md`.

---

Com isso fecham os três documentos da planta de hoje: `MODELO.md`,
`ARQUITETURA.md` e `MAPA-DE-TELAS.md`. Quer que eu revise algum ponto antes de
encerrarmos o dia 3, ou está tudo de acordo?
