# Como inserir o simulador no seu site

O simulador é 100% estático (HTML + CSS + JS em um único arquivo). Não precisa de backend, banco de dados ou build.

## Opção 1 — iframe (recomendada)

1. Publique o arquivo `index.html` em uma pasta pública do seu site, por exemplo:

   ```
   https://www.suaempresa.com.br/simulador/index.html
   ```

2. Na página onde deseja exibir o simulador, cole este código:

```html
<div class="simcef-wrap">
  <iframe
    id="simcef"
    src="/simulador/index.html"
    title="Simulador de Financiamento Imobiliário"
    loading="lazy"
    style="width:100%;min-height:900px;border:0;display:block">
  </iframe>
</div>

<script>
(function(){
  const frame = document.getElementById('simcef');
  window.addEventListener('message', function(e){
    if(e.data && e.data.tipo === 'simcef-altura' && e.source === frame.contentWindow){
      frame.style.height = (e.data.altura + 24) + 'px';
    }
  });
})();
</script>
```

O simulador avisa a página hospedeira sobre a altura necessária sempre que o cálculo, a aba ou a janela mudam. O `min-height:900px` serve como fallback até essa mensagem chegar.

**Se preferir altura fixa**, dispense o `<script>` e use apenas:

```html
<iframe src="/simulador/index.html" title="Simulador de Financiamento"
        width="100%" height="1200" style="border:0"></iframe>
```

> Se o iframe estiver em outro domínio, a comparação `e.source === frame.contentWindow` continua funcionando normalmente.

## Opção 2 — link ou botão

Ideal quando você quer abrir o simulador em uma página própria:

```html
<a href="/simulador/index.html" target="_blank" rel="noopener">
  Simular financiamento
</a>
```

## Opção 3 — incorporar diretamente na página (inline)

Copie o bloco `<style>` para o CSS do site, o conteúdo de `<div class="wrap">` para o local desejado e o bloco `<script>` logo antes de `</body>`.

**Atenção:** essa opção exige cuidado porque o script usa escopo global:

- Os `id`s (`valorImovel`, `renda`, `entrada`, etc.) não podem se repetir na página.
- O handler de `Enter` escuta `document`; pressionar Enter em qualquer campo do site dispara o cálculo. Restringa o seletor ao container do simulador.
- A classe `body.dark` alterna o tema do site inteiro; troque por uma classe no wrapper.
- As regras de `@media print` e `body.compact` também são globais.
- O app grava `simcef_tema` e `simcef_last` no `localStorage`.

Por esses motivos, **o iframe é a forma mais segura** de integrar sem afetar o restante do site.

## Publicação

Qualquer hospedagem de arquivos estáticos serve:

- Pasta do próprio site (FTP/cPanel/WordPress como arquivo estático);
- GitHub Pages, Netlify, Vercel, Cloudflare Pages etc.

O site deve ser servido via **HTTPS** (a API do BACEN também é HTTPS). Abrir o arquivo com `file://` funciona no seu computador, mas não para visitantes.

### Usando GitHub Pages + um botão no site (opção simples)

1. Crie um repositório no GitHub (pode ser público), por exemplo `simulador-financiamento`.
2. Faça upload de `index.html` para a **raiz** do repositório (o `README.md` e o `COMO_INSERIR.md` são opcionais).
3. No repositório, vá em **Settings → Pages**.
4. Em **Build and deployment**, selecione **Deploy from a branch** → branch `main` → pasta `/ (root)` → **Save**.
5. Aguarde 1–2 minutos. A URL será:

   ```
   https://SEU-USUARIO.github.io/simulador-financiamento/
   ```

   (se o repo for de uma organização: `https://SUA-ORG.github.io/simulador-financiamento/`)

6. No site da empresa, coloque apenas o botão:

```html
<a class="btn btn-primary"
   href="https://SEU-USUARIO.github.io/simulador-financiamento/"
   target="_blank"
   rel="noopener noreferrer">
  Simular financiamento
</a>
```

Use `target="_blank"` para abrir em nova aba e `rel="noopener noreferrer"` por segurança.

**Importante:** não use o link `github.com/usuario/repo/blob/main/index.html` nem o `raw.githubusercontent.com` — eles exibem o código-fonte, não uma página funcional. A URL válida é sempre a do `github.io`.

Se quiser trocar o endereço `github.io` por um domínio próprio (ex.: `simulador.suaempresa.com.br`), adicione um arquivo `CNAME` na raiz com o domínio e configure o DNS conforme a documentação do GitHub Pages.

**Limites:** GitHub Pages é gratuito para repositórios públicos e serve bem esse volume; o simulador roda inteiro no navegador e a chamada ao BACEN funciona normalmente em HTTPS.

## Verificação rápida

Depois de publicar:

1. Acesse `https://www.suaempresa.com.br/simulador/index.html` diretamente — o simulador deve aparecer.
2. Recarregue a página que contém o iframe — o simulador deve aparecer dentro dela.
3. Clique em **Atualizar taxa do BACEN** — o status deve mostrar a série e a data, ou uma mensagem de fallback se a rede bloquear a API.
