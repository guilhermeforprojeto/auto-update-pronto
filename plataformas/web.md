# Web (site, SPA, PWA)

O problema na web é o **cache**: o navegador (e o Service Worker, se houver) continua servindo arquivos antigos. A solução tem três partes.

## 1. Arquivos com hash e `index.html` sem cache

- O build gera nomes com hash (`app.3f9a1c.js`), padrão em Vite, Next.js, Angular e webpack.
- `index.html` (e `version.json`) com `Cache-Control: no-cache`. JS/CSS com hash com `Cache-Control: public, max-age=31536000, immutable`.

## 2. Detectar versão nova

**Com PWA / Service Worker** (Workbox, `vite-plugin-pwa`, `@angular/service-worker`):
- Use o modo de atualização com **aviso** (no `vite-plugin-pwa`: `registerType: 'prompt'`), não o de atualização automática silenciosa.
- Quando o novo Service Worker ficar em espera, mostre o aviso. Ao clicar em "Atualizar", envie a mensagem para ele assumir (`skipWaiting`) e recarregue a página.

**Sem Service Worker:**
- Gere um `version.json` no build com `buildId` (hash do commit ou data).
- Consulte ao abrir, a cada X minutos e no evento `visibilitychange` (quando o usuário volta para a aba).
- `buildId` diferente do carregado → mostre o aviso.

## 3. Aplicar sem atrapalhar

- Aviso discreto, fixo no canto: "Nova versão disponível · Atualizar".
- Recarregar sozinho **só** se a aba estiver ociosa há alguns minutos **e** não houver formulário sujo ou edição em andamento.
- Versão obrigatória (`minVersion`, ou a API responde que o cliente é antigo): bloqueie com uma tela explicando e um botão "Atualizar agora".
- Se o lazy-loading de um chunk falhar com 404 depois de um deploy, recarregue a página uma vez (é sinal de versão velha em memória).

## Logs

Envie para o seu monitoramento (Sentry, etc.) os eventos: versão nova detectada, atualização aceita, recarga automática, erro de chunk.

## Testes rápidos

1. Abra o app, faça um deploy com mudança visível, volte para a aba: o aviso aparece.
2. Clique em Atualizar: a mudança aparece e nenhum arquivo antigo é servido (aba Network/Application do DevTools).
3. Com um formulário preenchido, a página não recarrega sozinha.
