# ARQUITETURA.md — Onde cada coisa mora

Este documento explica **onde** cada parte do Periodiza vai rodar, quem fala
com quem, e — o mais importante — onde cada segredo (chave, senha) pode e não
pode aparecer. Ainda não existe código; isso é a planta de infraestrutura.

## As três partes do sistema

### 1. A tela (front estático, no navegador)
É o que você vê e toca no celular: o painel da semana, o treino do dia, o
Caderno de Treinos, o Painel de Evolução. É "estático" porque são só arquivos
prontos (HTML, o esqueleto da página; CSS, a aparência; JavaScript, o
comportamento) entregues do jeito que estão — não tem um servidor nosso
processando nada no meio do caminho. É **mobile-first**: desenhamos primeiro
pensando na tela do celular, porque é lá que você vive, e só depois — se
precisar — adaptamos para tela grande.

Esses arquivos ficam publicados no **Cloudflare** (um serviço que hospeda e
entrega essas páginas rápido, para qualquer lugar do mundo, de graça para o
tamanho do nosso projeto).

### 2. O banco de dados e o login (Supabase)
O **Supabase** guarda as tabelas do `MODELO.md` (o banco de dados de verdade)
e também vai cuidar do **login** (quem é você, quando isso existir). A tela no
navegador fala diretamente com o Supabase para ler e gravar os seus dados.

### 3. A Edge Function (o cofre da chave de IA, a partir do dia 9)
Uma **Edge Function** é um programinha pequeno que roda dentro da
infraestrutura do Supabase — **não no seu navegador**. Ela existe só para um
motivo: ser o único lugar autorizado a usar a chave secreta da IA. Quando a
tela precisar do único ponto de IA do sistema (ler o `texto_bruto` e devolver
os sinais extraídos), ela não vai chamar a IA diretamente — ela vai chamar a
Edge Function, e é a Edge Function quem chama a IA, usando a chave que só ela
conhece.

Hoje (dia 3) essa peça ainda não tem código — ela **nasce no dia 9**, junto
com a integração de IA. Estou registrando o lugar dela agora para o modelo já
prever isso.

## Onde cada segredo vive

| Chave | Onde mora | Por quê |
|---|---|---|
| `anon key` do Supabase | No navegador (é pública) | Ela é feita para ser pública: quem decide o que cada pessoa pode ler ou gravar são as regras de segurança do próprio banco (não a chave em si), então ela pode circular sem risco. |
| Chave da IA | Só dentro da Edge Function (é secreta) | Essa chave dá acesso a um serviço pago por uso — se ela vazasse no navegador, qualquer pessoa poderia copiá-la e gastar em nome da sua conta. Por isso ela nunca pode sair do servidor. |

Essa distinção é a mesma regra do `CLAUDE.md`: nenhuma chave secreta vai para o
navegador; segredo vive no servidor. A `anon key` não é exceção à regra — ela
simplesmente não é um segredo, é uma chave pública por desenho.

## O caminho dos dados (diagrama)

**Hoje (dia 3), sem IA ainda:**
```
[ Seu celular / navegador ]
          |
          |  (lê e grava dados, usando a anon key — pública)
          v
   [ Supabase: banco + login ]
```

**A partir do dia 9, com o único ponto de IA:**
```
[ Seu celular / navegador ]
          |
          |-- (lê e grava dados direto) ------------> [ Supabase: banco + login ]
          |
          |-- (manda o texto_bruto p/ interpretar) --> [ Edge Function ]
                                                              |
                                                              |  (chave da IA fica
                                                              |   trancada só aqui)
                                                              v
                                                        [ Serviço de IA ]
```

O navegador **nunca** fala diretamente com o serviço de IA — sempre passa pela
Edge Function, que é o único lugar onde a chave secreta existe.

## Regra de histórico: migrations

> **Toda mudança na estrutura do banco (criar tabela, adicionar campo, mudar
> tipo) vira um arquivo `.sql` novo, salvo na pasta `supabase/migrations/`, e
> esse arquivo é versionado no git junto com o resto do código.**

Cada arquivo é um passo datado do banco (ex: "criar tabela `usuario`", "criar
tabela `treino_registrado`"). Isso existe para você ter **histórico**: dá para
ver exatamente quando e por que cada tabela ou campo apareceu, e, se algo der
errado, dá para voltar atrás — porque cada passo está registrado, não
misturado direto no banco sem rastro.

---

Está de acordo com essa arquitetura? Posso seguir para o `MAPA-DE-TELAS.md`?
