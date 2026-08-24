# bravvostudios.com.br

O site da Bravvo Studios e dos jogos dela.

Um site só, dois mundos. HTML e CSS estáticos, sem build e sem framework.

```
index.html                        bravvostudios.com.br
robots.txt  sitemap.xml
pap/
  index.html                      /pap/
  politica-de-privacidade.html    /pap/politica-de-privacidade.html
  excluir-conta.html              /pap/excluir-conta.html
  pedir-exclusao.html             o formulário (não indexado)
  pedido-recebido.html            a confirmação (não indexada)
assets/
  bravvo.css                      o sistema do estúdio
  pap.css                         o sistema do jogo
  fontes/                         as fontes, hospedadas aqui
```

Repositório separado do jogo de propósito. O site muda por motivos que nada têm a ver
com o código (uma exigência da loja, um endereço de e-mail novo) e não deveria obrigar
ninguém a abrir o projeto do jogo para trocar um parágrafo.

## Acrescentar o segundo jogo

Uma pasta nova em `site/`, e um `<a class="plano plano--vivo jogo">` na grade de jogos da
raiz. A grade usa `auto-fill` com teto de coluna: o segundo cartão entra sem tocar em CSS.

Se o jogo novo tiver identidade própria, ele ganha o seu `assets/<jogo>.css`. Se não
tiver, herda `pap.css` ou o do estúdio.

## Por que estático, e não React

O site tem cinco páginas de texto. Um framework aqui seria toolchain para manter em
troca de nada, e duas destas páginas são **exigência do Google Play** — um arquivo
estático é a coisa mais confiável que existe para uma URL que precisa abrir sempre.

Acrescentar um jogo é copiar uma pasta. No dia em que o site tiver blog, catálogo ou
página por idioma, o Astro é o caminho: gera estático por padrão e monta componentes
React como ilhas, sem transformar o site inteiro num app.

## Os dois sistemas visuais

**A raiz é a forja.** Preto, metal escovado e cantos chanfrados, tirados da logo: uma
fera cortada em chapa facetada. Nenhum canto arredondado em lugar nenhum — `clip-path`
corta as quinas como chapa cortada, e ao passar o ponteiro a luz **rasa** o plano, que é
o gesto de girar uma peça de metal sob a lâmpada.

**A seção do jogo é o caderno.** Papel quadriculado, margem vermelha de caderno escolar,
e caixas com um ponto em cada canto — que no jogo são os botões.

Duas coisas atravessam os dois e fazem deles um site só: a fonte de corpo (IBM Plex
Sans) e o laranja `#c7512b`. No estúdio ele é aço na temperatura de trabalho; no jogo é
o traço do jogador um.

## As fontes ficam aqui, e não no Google

`assets/fontes/` tem os arquivos `.woff2` (400 KB, só os cortes latinos). Puxar de
`fonts.gstatic.com` entregaria o IP de cada visitante ao Google — e uma das páginas que
carrega esse CSS é justamente a que lista quais terceiros recebem dados seus.

Chakra Petch, IBM Plex e Poppins estão sob a SIL Open Font License, que permite
hospedagem própria.

## Largura

Não existe coluna estreita centralizada. O que tem teto é a **medida do texto** — grade
cresce, parágrafo não. Onde uma seção teria uma coluna de texto e um vazio ao lado, ela
vira duas colunas: rótulo e título à esquerda, conteúdo à direita. Nos documentos, a
largura vira índice fixo na lateral.

Conferido a 390, 768 e 1440 px.

## O formulário de exclusão

`pap/pedir-exclusao.html` é um formulário HTML comum que posta para um serviço de
retransmissão e devolve a pessoa para `pedido-recebido.html`. **Sem JavaScript**: uma
página que o Google Play revisa não deveria depender de script para funcionar.

Falta uma coisa para ele funcionar: a chave em `__CHAVE_DO_FORMULARIO__`.

1. Em [web3forms.com](https://web3forms.com), informe `contato@bravvostudios.com.br`.
   A chave chega por e-mail. Não precisa criar conta.
2. Troque `__CHAVE_DO_FORMULARIO__` pela chave.
3. Envie um teste e confira se chegou.

Para trocar de serviço (Formspree, Basin), muda o `action` do formulário e os campos
ocultos; os campos visíveis continuam iguais.

⚠️ **Quem receber os envios vira operador de dados** e precisa entrar na lista de
terceiros da política de privacidade, junto de Firebase, AdMob e Crashlytics. O e-mail
direto não tem esse custo, e continua oferecido na mesma página.

## Ser achado na busca

- `sitemap.xml` e `robots.txt` na raiz.
- `canonical` em toda página pública, para o Google não tratar `bravvostudios.com.br`,
  `www.` e `/index.html` como três páginas concorrentes.
- JSON-LD: `Organization` na raiz, `VideoGame` no jogo. É no `alternateName` que **PaP**
  entra, porque a sigla é curta demais para o buscador associar sozinho.
- O formulário e a confirmação levam `noindex`: página de fluxo na busca só atrapalha
  quem procura o jogo.

Depois de publicar, cadastre o domínio no Google Search Console e envie o sitemap. Sem
isso a indexação acontece, mas demora bem mais.

## Como publicar

Cloudflare Pages com envio direto: arraste a pasta `site/`. Sem git, sem build. Depois,
aponte o domínio em **Custom domains** e troque os servidores DNS no registro.br.

Detalhes e o passo a passo em `docs/lancamento.md`.
