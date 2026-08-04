# Task Estimation

Ferramenta web (single-page) para estimar tasks com a matriz **Complexidade × Incerteza**, curvas históricas de dias úteis e export para atualizar o Jira.

Arquivo principal: [`index.html`](./index.html).

## Como abrir

Não precisa de build. Basta servir os arquivos ou abrir no browser:

```bash
# na pasta do projeto
npx --yes serve .
# ou
python3 -m http.server 8080
```

Depois acesse `http://localhost:3000/` ou `http://localhost:8080/` (raiz do servidor).

> Abrir o HTML via `file://` funciona na maior parte dos casos; um servidor local evita restrições de alguns browsers com módulos/CDN.

## Abas

| Aba | Conteúdo |
| --- | --- |
| **Estimar** | Importa CSV, matriz, estimativa (dias + revisão), curvas e resultados salvos |
| **Sobre** | Explicação visual curta de como complexidade e incerteza viram dias |
| **Como decidir** | Guia: eixos, leitura da matriz, uso prático e regra de ouro |
| **Integrar com Jira** | Link direto para a lista JQL, passo a passo com screenshots (`assets/`), instrução/CSV/JSON para atualizar |

## Fluxo rápido

1. Clique em **+** e carregue um Export CSV do Jira (Issue key, Summary, Status, etc.).
2. Na matriz, escolha **complexidade** (linha) × **incerteza** (coluna).
3. Veja dias úteis de desenvolvimento, revisão/deploy e **tempo total** (com barra de erro da faixa típica).
4. Salve a estimativa (e comentário opcional para IA).
5. Na aba **Integrar com Jira**, copie a instrução / CSV / JSON para atualizar as tasks.

Estimativas e a sessão (lista de tasks carregadas) ficam em `localStorage` (`task-estimation-v1` e `task-estimation-session-v1`).

## Modelo mental

- **Complexidade** → quanto trabalho técnico existe → define a faixa histórica (ex.: P40–P95).
- **Incerteza** → quanto ainda não se sabe → desloca o ponto na faixa (baixa perto do P min; alta perto do P max).
- Célula da matriz → **story points** sugeridos.
- **Total** = desenvolvimento + revisão/deploy.

Regra de ouro: incerteza alta bloqueia estimativa; complexidade alta pede decomposição.

## Parâmetros de URL

| Parâmetro | Exemplo | Efeito |
| --- | --- | --- |
| `jiraUrl` | `?jiraUrl=https://seu-dominio.atlassian.net` | Base para links `/browse/<KEY>` e busca de issues (`jira` / `jira_base` ainda funcionam) |
| `jiraJql` | `?jiraJql=parent = KEY-123 ORDER BY created DESC` | JQL usada no link `{jiraUrl}/issues?jql=…` da aba Integrar com Jira |
| `tab` | `?tab=decidir` | Abre aba (`estimar`, `sobre`, `decidir`, `jira`) |
| `prop` | gerado pela própria UI | Persiste config do modelo (curvas, faixa, seleção) |

Valores de `jiraUrl` / `jiraJql` (e qualquer dado do time) vêm **somente** dos query params — não há default embutido no app. Exemplo:

```text
/?jiraUrl=https://seu-dominio.atlassian.net&jiraJql=parent%20%3D%20KEY-123%20ORDER%20BY%20created%20DESC&tab=estimar
```

## Configuração (⚙)

No modal ⚙ dá para editar tudo que vai na URL / `prop`:

- Query params: `jiraUrl`, `jiraJql`, `tab`
- Seleção da matriz (`complexity`, `uncertainty`, `uncertainty_anchor`)
- View (`y_scale`, `axis_mode`, `focus`, `curves_mode`, `review_stat`)
- Review em dias (`p40`, `median`, `p85`)
- Faixa típica (`band.min` / `band.max`)
- Matriz de story points e ranks finos
- Curvas de sizing (mediana e gaps)
- JSON completo do `prop` (avançado)

## Stack

- HTML + CSS + JS (sem framework)
- [p5.js](https://p5js.org/) — matriz interativa
- [Chart.js](https://www.chartjs.org/) + zoom — curvas de tempo
- [Papa Parse](https://www.papaparse.com/) — CSV do Jira
- Fontes: IBM Plex Sans / Mono (Google Fonts)

## Arquivos

```text
task-estimation/
├── index.html       # app completo
├── favicon.svg
├── assets/          # screenshots do tutorial Jira
└── README.md
```

## Licença / uso

Uso interno do time. Ajuste as curvas e a matriz conforme o histórico real do capítulo/domínio.
