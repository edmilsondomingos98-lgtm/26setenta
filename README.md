# Site 26SETENTA

Site estático de uma página só (`index.html` + `styles.css` + `script.js` + `assets/`). Não precisa de build nem de Node, por isso o deploy na Vercel é direto.

## 1. Publicar na Vercel

**Opção A, sem Git (mais rápida):**
1. Cria conta em vercel.com
2. No dashboard, "Add New" → "Project" → aba "Deploy" → arrasta a pasta `26setenta-site` inteira
3. A Vercel deteta que é um site estático (sem framework) e publica sem configuração nenhuma
4. Em "Settings" → "Domains", adiciona o domínio da 26SETENTA quando o tiveres

**Opção B, com GitHub (recomendada a longo prazo, permite atualizar por git push):**
1. Cria um repositório no GitHub e envia esta pasta
2. Na Vercel, "Add New" → "Project" → importa o repositório
3. Framework Preset: "Other" (site estático puro), Build Command e Output Directory ficam em branco
4. Deploy

## 2. Alojar os vídeos no Cloudflare R2

1. Cria conta em cloudflare.com, vai a "R2" e cria um bucket, por exemplo `26setenta-videos`
2. Nas definições do bucket, ativa o acesso público. Para produção, liga um domínio ou subdomínio próprio (ex: `videos.26setenta.com`) ao bucket em "Settings" → "Custom Domains". O domínio `r2.dev` que a Cloudflare dá por omissão serve para testar, mas tem limites de tráfego e a própria Cloudflare não o recomenda para produção
3. Antes de fazer upload, comprime os vídeos para não desperdiçares os 10GB grátis e para carregarem rápido. Um ponto de partida razoável em H.264, 1080p:
   ```
   ffmpeg -i original.mov -c:v libx264 -crf 20 -preset slow -c:a aac -b:a 128k -movflags +faststart video-final.mp4
   ```
   O `+faststart` é importante, permite que o vídeo comece a reproduzir antes de estar todo descarregado
4. Faz upload dos ficheiros `.mp4` finais para o bucket (dashboard da Cloudflare, ou o CLI `wrangler r2 object put`)
5. Copia o URL público de cada vídeo (o domínio que ligaste no passo 2, seguido do nome do ficheiro)
6. No `index.html`, cada trabalho tem um comentário a mostrar exatamente o que colar. Substitui o `<div class="work-frame">` pelo bloco `<video>` comentado ao lado, e cola o URL em `src`

Nota: o R2 sozinho serve o `.mp4` numa qualidade fixa (não é streaming adaptativo automático como YouTube ou Vimeo). Para o teu caso, um `.mp4` bem comprimido a 1080p resolve bem e sai grátis (o R2 não cobra tráfego de saída). Se mais tarde quiseres qualidade que se adapta sozinha à internet de quem vê, o passo seguinte é gerar várias resoluções com ffmpeg e servir via HLS, mas isso é opcional.

## Por preencher
- Logótipo: já incluído (`assets/26setenta-logo.png`)
- WhatsApp: já ligado (+244 934 746 668)
- Instagram e TikTok: já ligados
- YouTube: falta o link do canal (procurar `aria-label="YouTube"` no `index.html`)
- Vídeos dos trabalhos: por adicionar via Cloudflare R2, ver secção 2
