# MiniProj1: Currículo em Single Page Application (Vue.js)

**Autora:** Milena Maganin
**Unidade curricular:** Programação Web Avançada, Mestrado em Engenharia Informática e Tecnologia Web (UAb/UTAD)
**Tarefa:** 2.2, Miniprojeto SPA
**Repositório:** https://github.com/mimaganin/MiniProj1

## Objetivo

Site-currículo numa única página (`index.html`), desenvolvido como SPA com Vue.js, com três vistas: **"Quem Sou Eu?"**, **"O que faço?"** e **"Hobbies"**. A navegação entre vistas e a abertura de janelas modais fazem-se sem recarregar a página. O rodapé, presente em todas as vistas, mostra a identidade virtual com ícones de redes sociais.

## Como correr

1. Descarregar ou clonar o repositório.
2. Abrir o ficheiro `index.html` num browser (duplo clique).

Não é preciso instalar nada nem fazer build. O Vue 3 é carregado por CDN (versão fixa `3.4.38`), por isso é necessária ligação à Internet na primeira abertura.

Cada vista tem o seu endereço: `index.html#/quem-sou-eu` (vista por omissão), `index.html#/o-que-faco` e `index.html#/hobbies`.

## Estrutura

```
MiniProj1/
├── index.html            # a SPA completa: HTML (template), CSS e JavaScript
├── docs/
│   ├── wireframes.html   # wireframes de baixa fidelidade (fonte)
│   └── wireframes.pdf    # os mesmos wireframes em PDF (A4 horizontal, 5 páginas)
├── link-git.txt          # link do repositório (para o Moodle)
└── README.md
```

Dentro do `index.html`, o código está organizado por blocos comentados:

| Bloco | O que contém |
|---|---|
| `<style>` | Variáveis de design, base mobile first, cabeçalho, vistas, cartões, modal, acessibilidade e media queries |
| `<div id="app">` | Template do Vue: cabeçalho, `<main>` com as três vistas, modal e rodapé |
| `<script>` 1. Dados | Objeto `cv` com todo o conteúdo e lista `views` |
| `<script>` 2. Router | Função `getViewFromHash()` |
| `<script>` 3. App Vue | Estado reativo, gestão do hash, modal, teclado e ciclo de vida |

## Decisões técnicas e porquê

### 1. SPA: um documento carregado uma vez

Uma SPA é uma aplicação web em que o browser carrega **um único documento HTML** e, a partir daí, o conteúdo é atualizado por JavaScript, sem pedir páginas novas ao servidor (MDN, s.d.-b; Segue Technologies, 2013). Aqui o `index.html` é carregado uma vez; mudar de vista ou abrir um modal só altera partes do DOM.

### 2. Vue 3 por CDN, sem build

O enunciado pede que o site funcione a partir do `index.html`. Por isso usa-se o build global do Vue (`vue.global.prod.js`), que expõe o objeto `Vue` na janela. Não há npm, bundler nem servidor (Vue.js, s.d.-a). A versão é fixa para o projeto não mudar de comportamento se sair uma versão nova.

### 3. Estado reativo em vez de manipular o DOM à mão

A vista atual é guardada num `ref` (`currentView`). O template tem as três secções com `v-if` / `v-else-if`. Quando `currentView` muda, o Vue remove do DOM a secção antiga e cria a nova. O código nunca faz `innerHTML` nem `appendChild`: muda-se o **estado** e o Vue trata do **DOM** (Vue.js, s.d.-a). O título da vista é um `computed` que deriva do mesmo estado.

### 4. Conteúdo orientado a dados (data-driven)

Todo o texto do currículo está no objeto `cv`, no JavaScript. O HTML só define a estrutura e usa `v-for` para gerar listas, cronologias, cartões, etiquetas e ícones. Acrescentar um projeto, por exemplo, é só acrescentar um objeto ao array `cv.projects`. Os wireframes (página 1) mostram que campo do objeto alimenta cada zona de cada vista.

### 5. Hash routing (sem biblioteca)

Os links do menu apontam para fragmentos (`#/hobbies`). Mudar só o fragmento da URL **não recarrega a página**, mas cria uma entrada no histórico e dispara o evento `hashchange` (MDN, s.d.-a). A app ouve esse evento e atualiza `currentView`. Assim:

- os botões **Voltar/Avançar** do browser funcionam;
- cada vista tem um **link partilhável**;
- um fragmento inválido ou vazio é substituído (com `history.replaceState`, sem criar entrada no histórico) pela vista por omissão.

Para três vistas, a própria documentação do Vue sugere este "routing simples feito de raiz" em vez de instalar o Vue Router (Vue.js, s.d.-b).

### 6. Modal único e reutilizável

Há **um só** modal no HTML. Abrir um projeto ou um hobby guarda uma cópia desse item em `modalItem`; o modal é mostrado com `v-if="modalItem"` e preenchido com os dados do item. Fechar é pôr `modalItem` a `null`. O modal fecha com o botão ×, com a tecla Esc e com um clique no fundo escuro (`@click.self`). Se se carregar em Voltar com o modal aberto, ele também fecha.

### 7. Responsivo (mobile first)

O CSS base é para ecrãs pequenos. A partir de 600 px os cartões passam a 2 colunas e o modal passa de "folha" no fundo do ecrã a caixa centrada. A partir de 760 px o cabeçalho fica numa linha (nome à esquerda, menu à direita). O menu tem só 3 links, por isso fica sempre visível: no telemóvel passa para baixo do nome, sem menu hambúrguer.

### 8. Acessibilidade

- HTML semântico: `header`, `nav`, `main`, `section`, `footer`, listas `ul`/`ol`.
- `aria-current="page"` no link da vista ativa; o destaque visual usa o mesmo atributo no CSS, para estado e estilo nunca divergirem.
- Foco visível (`:focus-visible`) e link "Saltar para o conteúdo".
- Ao mudar de vista, o foco vai para o título `h2` da nova vista, para que o leitor de ecrã anuncie a mudança.
- Os cartões são `<button>`: são focáveis e funcionam com Enter/Espaço sem código extra.
- No modal: `role="dialog"`, `aria-modal="true"`, `aria-labelledby`, foco inicial no botão ×, Tab preso dentro do modal e foco devolvido ao cartão ao fechar (W3C WAI, s.d.).
- Ícones SVG com `aria-hidden="true"`; o nome da rede está no `aria-label` do link.
- Respeita `prefers-reduced-motion`.

### 9. Convenção de nomes

Todo o **código** está em inglês: classes e variáveis CSS, ids, comentários, nomes de variáveis, funções e campos dos dados (`currentView`, `openModal`, `cv.about.education`…). O **conteúdo** visível está em português (PT-PT). A única exceção são os ids das vistas usados na URL (`#/quem-sou-eu`, `#/o-que-faco`, `#/hobbies`): fazem parte do endereço que o visitante vê e partilha, por isso ficam na língua do site.

## O que acontece no browser (timeline)

1. **Carregamento.** O browser lê o `index.html`, carrega o Vue por CDN e aplica o CSS. O `#app` está escondido (`v-cloak`) para não se verem os `{{ }}` ainda por preencher.
2. **Montagem.** `createApp(...).mount('#app')` compila o template, lê o hash (ou define `#/quem-sou-eu`), gera o DOM da vista inicial e regista os ouvintes `hashchange` e `keydown`.
3. **Clique em "Hobbies".** O link muda a URL para `#/hobbies`, sem pedido ao servidor. O `hashchange` dispara e `currentView` passa a `'hobbies'`. O Vue retira do DOM a `<section>` anterior e insere a de Hobbies, e o menu atualiza o `aria-current`. O foco vai para o título.
4. **Clique num cartão.** `modalItem` recebe os dados do item e o Vue insere o modal no fim do `#app`, com uma animação curta. O foco vai para o botão ×.
5. **Esc / × / clique fora.** `modalItem` volta a `null`, o Vue remove o modal do DOM e o foco volta ao cartão.
6. **Voltar.** O browser repõe o hash anterior, dispara `hashchange` e o passo 3 repete-se ao contrário. A página nunca é recarregada.

## Wireframes

Em `docs/wireframes.pdf` (fonte: `docs/wireframes.html`), em 5 páginas:

1. Mapa de navegação e tabela de distribuição dos dados pelas vistas.
2. "Quem Sou Eu?": desktop e telemóvel.
3. "O que faço?": desktop e telemóvel.
4. "Hobbies": desktop e telemóvel.
5. Modal: desktop e telemóvel.

Cada página tem anotações numeradas: que link leva a quê, o que abre o modal e o que fica fixo.

## Referências

MDN Web Docs. (s.d.-a). *Window: hashchange event*. Mozilla. Consultado a 7 de novembro de 2026, em https://developer.mozilla.org/en-US/docs/Web/API/Window/hashchange_event

MDN Web Docs. (s.d.-b). *SPA (Single-page application)*. Mozilla. Consultado a 7 de novembro de 2026, em https://developer.mozilla.org/en-US/docs/Glossary/SPA

Queirós, R., & Portela, F. (2020). *Desenvolvimento avançado para a web: Do front-end ao back-end* (cap. 3). FCA.

Segue Technologies. (2013, 18 de abril). *What is a single page application?* https://www.seguetech.com/what-is-a-single-page-application/

Vue.js. (s.d.-a). *Introduction*. Consultado a 7 de novembro de 2026, em https://vuejs.org/guide/introduction.html

Vue.js. (s.d.-b). *Routing*. Consultado a 7 de novembro de 2026, em https://vuejs.org/guide/scaling-up/routing.html

W3C Web Accessibility Initiative. (s.d.). *Dialog (modal) pattern*. ARIA Authoring Practices Guide. Consultado a 7 de novembro de 2026, em https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/
