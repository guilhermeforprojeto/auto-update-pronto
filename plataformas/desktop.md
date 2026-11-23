# Desktop (Windows, macOS, Linux)

Use uma biblioteca de atualização pronta da sua tecnologia. **Não escreva um updater do zero**: baixar, verificar, substituir arquivos em uso e reiniciar com segurança é onde moram os bugs.

| Tecnologia do app | Ferramenta recomendada | Observações |
|---|---|---|
| **Electron** | `electron-updater` (do electron-builder) | Publica em GitHub Releases, S3 ou servidor próprio. Confere o hash do arquivo pelo `latest.yml`. No macOS o app precisa estar assinado e notarizado para atualizar |
| **Tauri** | Plugin oficial `updater` | Assinatura é **obrigatória** (gere o par de chaves com o CLI do Tauri e guarde a privada no CI) |
| **.NET (WinForms, WPF, Avalonia), C++, Rust, Python empacotado** | **Velopack** | Sucessor moderno do Squirrel. Instala sem admin, faz atualização incremental (delta) e tem SDK para várias linguagens |
| **Windows com MSIX** | Arquivo `.appinstaller` | Configure a verificação ao abrir e em segundo plano no próprio arquivo |
| **macOS nativo (Swift/AppKit)** | **Sparkle** | Usa appcast (XML) e assinatura EdDSA |

## Regras para qualquer uma

1. **Assine o app** (Windows: certificado de assinatura de código; macOS: Developer ID + notarização). Sem isso, o Windows SmartScreen e o Gatekeeper do macOS assustam o usuário a cada atualização.
2. **Chave privada só no CI** (GitHub Actions secrets, cofre). Nunca no repositório.
3. **Fluxo de UI:** verificar ao abrir → baixar em segundo plano → aviso "Atualização pronta · Reiniciar agora / Depois" → se "Depois", aplicar ao fechar o app.
4. **Apps de bandeja (tray)** que ficam abertos dias: verificar a cada 6 h e mostrar o aviso no menu da bandeja, não em janela que rouba o foco.
5. **Obrigatória:** ao abrir, se a versão for menor que `minVersion`, mostrar a janela de atualização antes do resto do app.
6. **Rollback:** marque a nova versão como "boa" depois que ela abrir com sucesso. O Velopack e o Sparkle mantêm a versão anterior; em Electron, publique rapidamente uma versão corrigida (o `electron-updater` não volta sozinho).
7. **Logs** em arquivo na pasta de dados do app (ex.: `%LOCALAPPDATA%\<App>\logs\updater.log`).

## Publicação (CI)

Automatize no CI a cada tag `vX.Y.Z`: build → assinatura → geração do manifesto com SHA-256 → upload para o GitHub Releases (ou bucket) → `rollout` inicial baixo.

## Testes rápidos

1. Instale a versão `1.0.0`. Publique a `1.0.1`. Abra o app: o aviso aparece e, ao reiniciar, a versão em "Sobre" é `1.0.1`.
2. Corrompa o arquivo publicado (ou o hash no manifesto): a atualização é recusada e a `1.0.0` continua abrindo.
3. Desligue a internet durante o download: o app segue funcionando e tenta de novo depois.
