---
name: Implementar atualização automática
about: Tarefa para implementar o auto-updater seguindo a especificação
title: "Implementar atualização automática (auto-updater)"
labels: enhancement
---

## Objetivo

Garantir que os usuários estejam sempre na versão mais recente de **[nome do sistema]**, recebendo correções e novidades de forma transparente, segura e sem interromper o trabalho.

## Especificação

Seguir a especificação em https://github.com/guilhermeforprojeto/auto-update-pronto (`SPEC.md` + guia da plataforma).

- **Plataforma:** [web / desktop Windows / desktop macOS / Android / iOS]
- **Tecnologia:** [ex.: Electron, Tauri, .NET, React + Vite, Expo, Flutter]
- **Onde publicamos hoje:** [ex.: GitHub Releases, Play Store, Vercel]

## Fluxo esperado (resumo)

1. Verificar versão ao abrir e a cada 6 h (web: também ao voltar para a aba).
2. Diferenciar opcional de obrigatória pelo `minVersion` do manifesto.
3. Baixar em segundo plano sem travar a interface.
4. Conferir SHA-256 (e assinatura, no desktop) antes de aplicar.
5. Aplicar sem perder trabalho do usuário ("Reiniciar para atualizar" / aviso na web).
6. Em qualquer falha: manter a versão atual funcionando e tentar de novo depois.
7. Registrar cada etapa em log.

## Critérios de aceite

Todos os itens do `CHECKLIST.md` marcados, com evidência no PR.

## Responsabilidades fora do código

- [ ] Certificado de assinatura de código / conta de desenvolvedor: **[quem providencia]**
- [ ] Segredos cadastrados no CI: **[quem cadastra]**
