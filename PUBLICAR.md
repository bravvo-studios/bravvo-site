# Publicar o site

Do repositório local até `bravvostudios.com.br` no ar, com HTTPS.

Cinco partes. A ordem importa: a parte 2 é a que pode derrubar o e-mail do estúdio se
for feita fora de hora.

---

## 1. Subir o repositório para o GitHub

O Cloudflare Pages precisa de um repositório para acompanhar. Sem isso, dá para fazer
envio direto (parte 3, alternativa B), mas aí cada atualização vira arrastar pasta na
mão.

```bash
gh auth login
cd C:\Users\Thomass\Desktop\Projetos\bravvo-site
gh repo create bravvo-site --public --source=. --push
```

**Público ou privado?** Tanto faz para o Cloudflare. O conteúdo já é público (é um site),
e público facilita se um dia alguém quiser apontar um erro de texto.

**Conferir:** `gh repo view --web` abre o repositório no navegador. Se os arquivos
estiverem lá, essa parte acabou.

---

## 2. Pôr o domínio no Cloudflare

⚠️ **Leia inteiro antes de clicar em qualquer coisa.** Esta é a parte que quebra e-mail.

Você tem `contato@bravvostudios.com.br` funcionando hoje. Esse e-mail depende de
registros **MX** no DNS do domínio. Ao mudar os servidores de nomes para o Cloudflare,
o DNS que passa a valer é o de lá. Se os MX não tiverem sido copiados, **o e-mail para
de chegar, e você não recebe aviso nenhum.** Some em silêncio.

O Cloudflare tenta importar os registros sozinho ao adicionar o domínio. Ele acerta
quase sempre. "Quase" é o problema.

### O passo a passo

1. Crie a conta em [dash.cloudflare.com](https://dash.cloudflare.com) (o plano grátis
   basta para tudo aqui).
2. **Add a domain** → digite `bravvostudios.com.br` → escolha o plano **Free**.
3. O Cloudflare varre o DNS atual e mostra o que encontrou.

4. **Pare aqui e confira a lista.** Procure as linhas com tipo **MX**. Compare com o que
   existe hoje no painel do seu provedor de e-mail ou no registro.br. Se faltar alguma,
   acrescente à mão antes de seguir.

   Se o seu e-mail for de um provedor conhecido, os MX costumam ser assim:

   | Provedor | O que procurar |
   |---|---|
   | Google Workspace | `smtp.google.com` ou `aspmx.l.google.com` |
   | Zoho | `mx.zoho.com`, `mx2.zoho.com` |
   | Titan / HostGator / Locaweb | algo com `titan.email` ou o domínio do provedor |
   | E-mail do próprio registro.br | os MX que o painel de lá mostrar |

   Anote também qualquer **TXT** com `v=spf1` e qualquer **CNAME** ou **TXT** de DKIM.
   São eles que impedem o seu e-mail de cair no spam alheio.

5. Só depois disso, siga para a parte 3.

**Como conferir sem risco:** antes de trocar qualquer coisa, rode isto e guarde o
resultado. É a sua rede de segurança.

```bash
nslookup -type=MX bravvostudios.com.br
nslookup -type=TXT bravvostudios.com.br
```

---

## 3. Trocar os servidores de nomes no registro.br

O Cloudflare vai te dar **dois** servidores, parecidos com
`ana.ns.cloudflare.com` e `rick.ns.cloudflare.com`. Os nomes mudam por conta.

1. Entre em [registro.br](https://registro.br) e abra o domínio.
2. Procure **Alterar servidores DNS** (às vezes aparece como "DNS" ou "Servidores DNS").
3. Apague os que estão lá e ponha os dois do Cloudflare.
4. Salve.

**Quanto demora:** de alguns minutos a algumas horas. O registro.br costuma ser rápido.
Pode levar até um dia em casos ruins.

O Cloudflare manda um e-mail quando reconhece a troca, e o painel mostra o domínio como
**Active**.

**Conferir:** repita o `nslookup -type=MX` da parte 2. O resultado tem que ser **igual**
ao que você anotou. Se mudou ou sumiu, volte ao DNS do Cloudflare e corrija antes de
seguir. Um e-mail perdido não volta.

---

## 4. Criar o projeto no Pages

### Alternativa A: conectado ao repositório (o normal)

1. No painel, **Workers & Pages** → **Create** → aba **Pages** → **Connect to Git**.
2. Autorize o GitHub e escolha `bravvo-site`.
3. Nas configurações de build:

   | Campo | Valor |
   |---|---|
   | Framework preset | **None** |
   | Build command | *(deixe vazio)* |
   | Build output directory | `/` |
   | Root directory | *(deixe vazio)* |

   O site é HTML e CSS servidos como estão. Não há o que compilar, e um build command
   preenchido à toa só cria uma etapa que pode falhar.

4. **Save and Deploy.**

Em menos de um minuto ele publica em algo como `bravvo-site.pages.dev`.
**Abra esse endereço e confira o site inteiro antes de mexer no domínio.** É de graça
errar aqui.

Daqui em diante, todo `git push` republica sozinho.

### Alternativa B: envio direto (sem GitHub)

Se você quiser ver o site no ar agora e deixar o GitHub para depois:

**Workers & Pages** → **Create** → **Pages** → **Upload assets** → arraste a pasta
`bravvo-site`. Sem git, sem build, sem linha de comando.

Dá para migrar para a alternativa A depois sem perder o domínio.

---

## 5. Ligar o domínio

1. No projeto do Pages, aba **Custom domains** → **Set up a custom domain**.
2. Digite `bravvostudios.com.br` e confirme.
3. Repita para `www.bravvostudios.com.br`.

Como o domínio já está no Cloudflare (parte 2), ele cria os registros sozinho. O
certificado HTTPS sai em alguns minutos.

**O `www` importa.** Gente digita com e sem. Com os dois cadastrados, o Cloudflare
redireciona um para o outro e o Google não trata como dois sites concorrentes.

**Conferir:**

- `https://bravvostudios.com.br` abre com cadeado
- `https://bravvostudios.com.br/pap/` abre
- `https://bravvostudios.com.br/pap/politica-de-privacidade.html` abre
- `https://bravvostudios.com.br/robots.txt` mostra texto

A política precisa abrir em `https://` sem login. É isso que a Play verifica.

---

## Depois que estiver no ar

**A chave do formulário.** Enquanto `__CHAVE_DO_FORMULARIO__` estiver em
`pap/pedir-exclusao.html`, o envio falha. Peça a chave em
[web3forms.com](https://web3forms.com) informando `contato@bravvostudios.com.br`,
troque no arquivo, `git push`. E **envie um teste** para confirmar que chega.

**Search Console.** Cadastre o domínio em
[search.google.com/search-console](https://search.google.com/search-console) e envie
`https://bravvostudios.com.br/sitemap.xml`. A indexação acontece sem isso, mas demora
bem mais.

**As duas URLs que vão para o Play Console:**

```
https://bravvostudios.com.br/pap/politica-de-privacidade.html
https://bravvostudios.com.br/pap/excluir-conta.html
```

---

## Se der errado

| Sintoma | O que olhar |
|---|---|
| O site abre sem estilo | O `Build output directory` não é `/`. Corrija e refaça o deploy. |
| `/pap/` dá 404 | O deploy não subiu a pasta. Confira se `pap/index.html` está no commit. |
| O domínio não conecta | O registro.br ainda não propagou. Espere e tente de novo. |
| Parou de chegar e-mail | **Volte ao DNS do Cloudflare e confira os MX.** Ver a parte 2. |
| O formulário não envia | A chave ainda é o marcador, ou o e-mail do Web3Forms não foi confirmado. |
