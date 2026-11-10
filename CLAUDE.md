# TurboCut — Contexto para Claude Code

> Leia este arquivo antes de qualquer coisa. É o ponto de partida de toda sessão.
> Ele é **índice**, não manual: detalhes vivem em [FORK.md](FORK.md), no upstream
> ([docs/BUILDING.md](docs/BUILDING.md)) e — para sessões do mantenedor — na pasta
> local `_planejamento/` (privada, fora deste repositório público; colaboradores
> externos: contribuam via issues, e sejam bem-vindos).

## O que é o TurboCut

Fork brasileiro do [CutWire Drift](https://github.com/CutWire-Studios/Drift)
(editor de vídeo desktop, C++/Qt 6/FFmpeg, GPL-3.0), mantido por Alex Bassani.
Hoje o delta é marca, tradução pt-BR, correções de plataforma e empacotamento — e, desde
20/09/2026, **pode ganhar função nossa quando o Alex decidir** (Regra #0). O motor nasce no
upstream; a identidade, a distribuição e o rumo são nossos.

## 🧭 Regra #0 (acima de todas): o TurboCut segue com o Drift — do NOSSO jeito, e quem decide é o Alex

> **REVOGADA em 20/09/2026 a regra antiga** (*"o delta é sagrado — NUNCA criar feature de edição no fork"*).
> Ela nasceu em 27–28/08/2026 para o fork sair em dois dias, ficou sem prazo e sem revisão, e passou a travar o
> mantenedor. Decisão do Alex: **seguimos com o Drift, do nosso jeito — sem depender de olhar lá todo dia.**
> A licença (GPL-3.0) só pede uma coisa: quem distribui o programa publica o código — e este repositório já é
> público. **Nada nela, nem no projeto original, proíbe função nossa.**

1. **Função própria é PERMITIDA. A decisão é do Alex, nunca de uma regra.** Quando ele pedir algo que o Drift não
   tem, a resposta NÃO é "isso vai para o upstream": é o custo dito em UMA linha (*"dá para fazer aqui; isto passa a
   ser reaplicado a cada sync"*) — e ele decide.
2. **O jeito cuidadoso de fazer:** passo pequeno, em ARQUIVO NOVO sempre que der (arquivo nosso não conflita no
   sync), tocando o mínimo do que é deles; registrado na tabela do [FORK.md](FORK.md); oferecido ao upstream como
   PR **por cortesia** — se eles adotarem, o nosso delta encolhe sozinho (foi o que houve com o conserto do FreeType).
3. **Antes de escrever, OLHAR o que já veio de lá** (Regra #0.2) — a v0.6.0 entregou, de graça, quase a lista de
   desejos inteira do Alex. Sincronizar costuma ser mais barato que escrever.
4. **Continua valendo: não refatorar o motor deles por gosto.** Reorganizar código alheio não dá nada ao usuário e
   torna todo sync um inferno. Função nova, sim; faxina no que é deles, não.
5. **Exclusividade para aluno mora FORA deste binário** (TurboStudio, MCP, conteúdo): o que entra aqui é GPL e público.

### 🔭 Regra #0.2 — ninguém precisa "lembrar de olhar o Drift": o passo 0 de TODA sessão é este comando

```bash
gh release list -R CutWire-Studios/Drift --limit 3 && gh release list -R cpmdark/turbocut-cutwire --limit 1
```

Se o Drift estiver à frente, **dizer ao Alex na primeira resposta**, com o resumo do que veio
(`gh release view vX.Y.Z -R CutWire-Studios/Drift`), e só então seguir com o pedido dele. Em 20/09/2026 a v0.6.0
estava no ar havia 7 dias sem ninguém ter visto — com a camada de ajuste (ideia dele), colar atributos na seleção,
seleção múltipla e pastas na biblioteca. **Sincronizar é escolha NOSSA** (quando vier algo que a gente queira ou um
conserto que proteja o usuário), não obrigação diária.

### ⛔ Regra #0.1 — a base é a TAG de release, NUNCA o main do upstream

Bug real (28/08/2026): o 1º build nasceu do `main` do Drift e renderizava a UI com
glifos corrompidos (feature não-lançada de UI scale). O `main` deles é canteiro de
obras; **todo sync parte da última tag `vX.Y.Z`**.

## Regras de ouro (NUNCA violar)

1. **Assets de marca trocam CONTEÚDO, nunca NOME** — `Drift_icon.png`,
   `resources/drift.png`, `resources/windows/drift.ico` seguem com esses nomes
   (delta zero em código/CMake).
2. **AppId do Inno Setup NUNCA muda**: `{DC0EC0C4-090C-4519-A3EF-33E9B201A062}` —
   é o que faz o Setup atualizar in-place (e o card do TurboStudio depende dele
   no registro).
3. **Formato de projeto `.drift` é intocável** — compatibilidade total com o
   upstream, projetos abrem nos dois editores.
4. **Catálogo pt-BR no formato canônico do lupdate**, senão o teste `Translations`
   reprova (2 incidentes reais em 28/08/2026): `numerusform` um por linha
   (multi-linha) e aspas duplas como `&quot;` dentro de `<translation>`.
5. **`sourcelanguage="en_US"`** no cabeçalho do catálogo (o lupdate normaliza).
6. **Versão: os três SEMPRE iguais** — `project(Drift VERSION X.Y.Z)` == `pkgver`
   do PKGBUILD == tag (o release.yml recusa se divergir). A base vem do Drift;
   correções só nossas entre releases deles ganham **patch próprio** (foi o caso
   da `0.4.1`, com o runtime do Visual C++). Quando o Drift lançar `0.5.0`,
   voltamos a acompanhar a base deles.
7. **O fix FreeType do Windows fica** (`src/main.cpp`, `QT_QPA_PLATFORM`
   `windows:fontengine=freetype`): o DirectWrite corrompe glifos de fontes
   embutidas em certas GPUs/escalas. Respeita env já definido.
8. Textos MCP ficam em inglês (contrato de máquina — regra do upstream).

## O delta completo (onde a marca mora)

Ver [FORK.md](FORK.md) — tabela arquivo a arquivo. Resumo: `src/app/main.cpp` (nomes),
`src/app/StartupSplash.cpp` (nome no splash do Drift), `src/qml/Main.qml` e
`AndroidMain.qml` (título), `src/app/online/updates/UpdateAsset.cpp` (aceita
`TurboCut-Setup-*`), `installer/windows/Drift.iss`, `CMakeLists.txt` (feed de update →
este repo), `.github/workflows/` (artefatos TurboCut-* + `build-windows.yml` manual),
`i18n/drift_pt_BR.ts`, `packaging/arch/PKGBUILD` (fonte = este repo), `branding/`.

## Workflow de sync (quando o Drift lançar versão nova)

1. Tags do Drift com PREFIXO (nunca `--tags` puro — armadilha nº 1):
   `git fetch upstream --no-tags "refs/tags/vX.Y.Z:refs/tags/up-vX.Y.Z"` + branch `backup/pre-sync-X.Y.Z`.
2. Rebase dos nossos commits sobre a NOVA TAG (`git rebase --onto up-vX.Y.Z up-v<antiga> main`).
   Conflitos esperados: instalador, `package.yml`, PKGBUILD, metainfo, README; `main.cpp` se
   mexerem no boot. Nos commits de catálogo intermediários, ficar com o DELES (`checkout --ours`):
   o catálogo é remontado de uma vez no passo 3.
3. Catálogo pt-BR (o Drift mantém pt-BR próprio desde a 0.7.5): `_planejamento/scripts/merge_ptbr.py`
   (base = deles; por cima as nossas escolhas e o que eles deixaram em branco; gera
   `pendentes.json`) → traduzir os pendentes → `apply_ptbr.py` (valida placeholders/tags/plurais
   e grava no formato canônico). O `remap-ptbr.ps1` é do tempo em que não havia pt-BR deles.
4. Conferir regras de ouro 4–6. Push numa branch `sync/X.Y.Z` (push normal, sem trava) para
   testar; o `main` só leva force-push depois de validado (o Alex solta a trava).
5. O workflow **Tests** roda sozinho no push (`Translations` é o guardião do catálogo).
6. Build de teste: `gh workflow run build-windows.yml -R cpmdark/turbocut-cutwire --ref sync/X.Y.Z -f version=X.Y.Z-turboN`.
7. Validou? Tag `vX.Y.Z` → release.yml gera TODAS as plataformas e publica a
   release (o aviso de atualização in-app e o card do TurboStudio leem daqui).
8. Atualizar `## Estado atual` (abaixo) e o backlog privado.

## Estado atual (atualizado 10/11/2026 — v0.9.1 LANCADA)

- **🎉 v0.9.1 NO AR** (10/11): https://github.com/cpmdark/turbocut-cutwire/releases/tag/v0.9.1
  Release verde de PRIMEIRA em todas as plataformas (2h07). Instalador: 130 -> 147 MB.
  Título corrigido à mão (8-B); `releases/latest` responde 0.9.1 e o `TurboCut-Setup` tem
  digest SHA-256 (o atualizador do app exige). Tests 30/30 e build `0.9.1-turbo1` verdes antes.
  Pula a 0.8.0 (13/10) e a 0.9.0 (01/11): **469 commits deles / 2.298 arquivos** — o maior
  até hoje. Delta: 30 commits. Backup: `backup/pre-sync-0.9.1`; tags `up-v0.8.0/0.9.0/0.9.1`.
  - **Force-push:** o "Allow force pushes" do `main` ficou LIGADO por escolha do Alex (09/11).
    A branch `sync/0.9.1` (teste) segue no origin.
  - **Decisões do Alex (09/11):** (1) splash: ficou o do PRÓPRIO Drift (`StartupSplash`, com
    barra de progresso, já desenha `drift.png`), só o nome vira "TurboCut" — a `TurboSplash`
    e o `resources/splash.png` saíram; (2) o atualizador do app (que desde a 0.8.0 baixa,
    confere SHA-256 e instala sozinho) aceita `TurboCut-Setup-*` — 1 linha em
    `UpdateAsset.cpp` + teste em `tst_updateasset.cpp`. Mac/Linux já funcionavam (dmg e
    AppImage seguem `Drift-*`).
  - **Arquivos que MUDARAM DE LUGAR no Drift:** `src/main.cpp` → `src/app/main.cpp`,
    `src/HeadlessApp.cpp` → `src/app/HeadlessApp.cpp`, `DebugInfoDialog.qml` →
    `components/dialogs/` (e o link de bug agora existe também no `HelpDialog.qml`).
  - Instalador: arch vira parâmetro (`/DArm64`); o nosso runtime do Visual C++ passou a
    ser `vc_redist.<arch>.exe`. O Windows ARM vem DESLIGADO no `package.yml` deles
    (skia[gl] não suporta) — nada a fazer agora.
  - `build-windows.yml` precisou de `actions: write` (startup_failure sem isso).
  - Catálogo: 6.208 mensagens. Base deles + 262 escolhas nossas + 1.186 novas traduzidas
    aqui (4 lotes em paralelo, termos padronizados: Mercado, quadros-chave, espaços de
    trabalho, manipulador 3D, Source Lab). 0 pendentes, `en_US`, recuo 12/8 conferido.
  - ⚠️ Ainda é do Drift: o ícone de macOS novo (`resources/macos/Drift.icon/`, SVG — o nosso
    logo oficial é PNG; precisa de um SVG da marca) e o título da release ("Drift vX" —
    corrigir à mão, 8-B). Projeto salvo na 0.9.1 (histórico de desfazer no arquivo)
    provavelmente não abre mais na 0.7.5.

### Historico: v0.7.5 (12/10/2026)

- **🎉 v0.7.5 NO AR** (12/10): https://github.com/cpmdark/turbocut-cutwire/releases/tag/v0.7.5
  Sync sobre a tag `v0.7.5` do Drift (05/10): **265 commits deles / 563 arquivos** —
  maior que o da 0.6.0. Pula a 0.7.0 (28/09). Release verde de PRIMEIRA em todas as
  plataformas (33 min). Instalador: 126 -> 130 MB. Titulo corrigido a mao (8-B);
  feed e landing (`releases/latest`) ja respondem 0.7.5.
  Backup do main antigo: branch local `backup/pre-sync-0.7.5`. Tags do upstream
  buscadas como `up-vX.Y.Z` (armadilha nº 1 evitada na origem — melhor que apagar
  e recriar depois).
  - **Build de teste lento (88 min):** o cache vcpkg do Windows tinha expirado (o GitHub
    apaga cache sem uso por 7 dias) e compilou OpenSSL & cia do zero. Normal; a release
    seguinte reaproveitou.
  - **Novo no release.yml:** o job "Dispatch store builds" dispara `msstore.yml` e
    `playstore-aab.yml` depois de publicar. So geram artefatos (nada vai para loja,
    sem os segredos deles) — inofensivos.
  - Delta: **25 commits**. Conflitos: `Drift.iss` e `package.yml` (eles criaram o
    **canal Nightly** — instalador com AppId proprio; o nosso nightly ganhou AppId
    NOVO `96B3680B-...`, o estavel segue `DC0EC0C4-...`), catalogo e metainfo.
  - **Catalogo: o Drift passou a manter a PROPRIA traducao pt-BR** (adotaram a
    nossa e seguiram traduzindo). Base = a deles; por cima, as **384** escolhas nossas
    que divergiam (ex.: "Ligue" x "Ative", "Pronto" x "Concluir") + **241 strings
    novas** traduzidas aqui. 3.697 mensagens, 0 pendentes, cabecalho `en_US`.
  - `nightly.yml` (cron diario) ja vem com guarda: so roda agendado no repo do Drift.
    `release.yml` ganhou Homebrew e Discord — ambos `continue-on-error`, pulam sem
    segredo. Nada a mudar.
  - ⚠️ **Licao de processo:** o Bash do Windows (Git Bash) colapsa `\\` em heredoc
    de Python e o `/tmp` dele nao e o do Python — um script falhou em silencio e o
    commit seguinte levou MARCADORES DE CONFLITO (pego e emendado antes do push).
    Scripts de resolucao: arquivo no scratchpad + `&&` em TUDO + `git grep "^<<<<<<< "`
    antes de seguir.
  - Pendente fora do sync: o modo `--headless` (`src/HeadlessApp.cpp`) ainda usa a
    pasta "CutWire Drift" — ja era assim na 0.6.0.

### Historico: v0.6.0 (21/09/2026)

- **🎉 v0.6.0 NO AR** (21/09): https://github.com/cpmdark/turbocut-cutwire/releases/tag/v0.6.0
  Sync de **143 commits deles / 445 arquivos** sobre a base v0.5.1. Release verde
  de PRIMEIRA em todas as plataformas (Flatpak incluso). Instalador: 110 -> 126 MB.
  - Entrega ao usuario: **camada de ajuste** (ideia do Alex, issue #154), **colar
    atributos com escolha do que levar em varios clipes**, importar PASTA, selecao
    multipla e pastas aninhadas na biblioteca, recorte de pessoa, estilos de texto
    em camadas com gradiente, ~50 animacoes de texto, Lottie/SVG, 100+ transicoes.
- **⚠️ REGRA DE OURO 7 VIROU HISTORICA:** o fix FreeType SUMIU da v0.6.0 e esta
  CERTO que suma — eles consertaram a causa raiz no commit `d9bc922` (a fonte
  variavel `Inter.ttf` corrompia glifos; trocada por instancias estaticas
  `InterUI-*`). Nao reintroduzir. A issue deles (#162) foi reportada EM PORTUGUES
  ("Letras sobrepostas impossibilitando a leitura") — possivelmente usuario nosso.
- **🔴 ARMADILHA NOVA DO CATALOGO (regra de ouro 4):** os blocos `numerus` precisam
  do recuo CANONICO do lupdate — `<numerusform>` com **12 espacos** e
  `</translation>` com 8. Gerei com 8/4 e o teste `Translations` teria reprovado;
  peguei comparando com `git show vX.Y.Z:i18n/drift_pt_BR.ts` ANTES de commitar.
  **Conferir sempre contra o arquivo do upstream, nao de memoria.**
- **As 3 armadilhas do release seguem valendo** — a nº 1 (tag do upstream com o
  MESMO nome) mordeu de novo e foi pega antes do push; a nº 2 (force-push × branch
  protection) exigiu o Alex ligar "Allow force pushes" (soltar a trava e bloqueado
  para a IA; **recolocar passa** e foi feito automaticamente); a nº 3 (uma falha
  pula a publicacao) nao apareceu desta vez.
- **v0.5.1** foi a anterior (conserto do piscar preto na NVIDIA).
- **Repo na organizacao `cpmdark`**; landing nos 2 dominios com o endereco novo.
- **Upstream:** das issues do Alex so a **#125** (Dip to Black) segue aberta — ele
  precisa retestar com outro clipe, o mantenedor nao reproduziu. #154, #123, #100,
  #127, #115, #116 fechadas; traducao pt-BR mergeada (#122).
- Pendencias e backlog: `_planejamento/BACKLOG_PROXIMAS_TAREFAS.md` (privado).

## Checklist antes de qualquer alteração

- [ ] Rodei o passo 0 (Regra #0.2) e disse ao Alex se o Drift está à frente?
- [ ] É função nova? → conferir se o Drift JÁ entregou; se não, dizer o custo em uma linha e o Alex decide (Regra #0). Arquivo novo sempre que der; registrar no FORK.md.
- [ ] Toquei em catálogo? → regras de ouro 4–5.
- [ ] Toquei em asset? → mesmo nome de arquivo (regra 1).
- [ ] Vou mexer no TurboStudio? → PARE: avisar o Alex antes, protocolo da bancada.
- [ ] Terminei algo? → backlog privado e `## Estado atual` atualizados junto.
