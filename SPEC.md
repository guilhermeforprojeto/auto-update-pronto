# Especificação: atualização automática (auto-updater)

## Objetivo

Garantir que os usuários estejam sempre na versão mais recente do sistema, recebendo correções e novidades de forma **transparente, segura e com o mínimo de interrupção**, sem nunca deixar o sistema quebrado por causa de uma atualização.

## Fluxo

1. **Verificação.** Ao abrir o app e depois em intervalos (padrão: a cada 6 h; web: também ao voltar para a aba), o sistema consulta o **manifesto de versão** no servidor e compara com a versão instalada.
2. **Decisão.**
   - Versão do servidor maior que a instalada → há atualização.
   - Versão instalada **menor que `minVersion`** do manifesto → atualização **obrigatória** (ex.: falha de segurança, API incompatível).
   - Caso contrário → atualização **opcional**.
3. **Download** em segundo plano, sem travar a interface. Progresso visível só quando fizer sentido (instaladores grandes).
4. **Verificação de integridade.** Antes de aplicar, conferir o **SHA-256** do arquivo e, em desktop, a **assinatura digital**. Se não bater, descartar o arquivo, registrar o erro e não instalar.
5. **Aplicação.**
   - **Desktop:** aplicar ao fechar o app, ou oferecer "Reiniciar para atualizar". Nunca reiniciar sozinho com trabalho não salvo.
   - **Mobile:** loja para atualizações de código nativo; OTA só para JS/assets/regras quando a plataforma permite.
   - **Web (PWA/SPA):** aviso discreto "Nova versão disponível — Atualizar". Recarregar sozinho só se a aba estiver ociosa e **sem formulário ou edição em andamento**.
   - **Obrigatória:** bloquear o uso com uma tela clara explicando o motivo e um botão para atualizar.
6. **Plano B.** Qualquer falha (sem internet, servidor fora, arquivo corrompido, disco cheio) mantém a versão atual funcionando e tenta de novo depois, com espera crescente (ex.: 1 min, 5 min, 30 min, 6 h).
7. **Pós-atualização.** No primeiro início da nova versão, confirmar que ela abriu corretamente. Se abrir com falha repetida, voltar para a versão anterior (**rollback**) quando a plataforma permitir.

## Manifesto de versão

Arquivo publicado junto com cada release (ver [`exemplos/version.json`](exemplos/version.json)):

| Campo | Para quê |
|---|---|
| `version` | Versão mais recente, em SemVer (`1.4.2`) |
| `minVersion` | Abaixo disso, a atualização é obrigatória |
| `publishedAt` | Data da publicação (ISO 8601) |
| `notes` | Novidades em texto curto, para mostrar ao usuário |
| `files[]` | Por plataforma/arquitetura: `url`, `sha256`, `size`, `signature` (quando houver) |
| `rollout` | Porcentagem de usuários que recebem (0–100), para liberar aos poucos |

Servido por HTTPS, sem cache (`Cache-Control: no-cache`), de um endereço controlado pelo time (ex.: GitHub Releases, bucket com CDN).

## Requisitos

- **SemVer** (`MAJOR.MINOR.PATCH`). A versão vem de uma única fonte no projeto (ex.: `package.json`, `.csproj`, `pubspec.yaml`) e aparece na tela "Sobre".
- **HTTPS obrigatório** para manifesto e arquivos.
- **Integridade:** SHA-256 sempre; assinatura digital em desktop. A chave privada de assinatura **nunca** fica no repositório nem no app: só no CI ou no cofre de segredos.
- **Sem downgrade:** nunca instalar versão menor que a atual, mesmo que o manifesto mande.
- **Logs:** registrar verificação, download, verificação de hash, sucesso ou erro (com o motivo), versão antiga e nova. Sem dados pessoais nos logs.
- **Configurável:** opção para desativar a verificação automática (ambientes corporativos) e checar manualmente em "Sobre → Verificar atualizações".
- **Liberação gradual (recomendado):** usar `rollout` para liberar para 10%, depois 50%, depois 100%.

## Critérios de aceite

Ver [`CHECKLIST.md`](CHECKLIST.md). Resumo:

- Detecta corretamente quando há versão maior no servidor e ignora versão igual ou menor.
- Diferencia atualização opcional de obrigatória (`minVersion`).
- O download não trava a interface.
- Arquivo com hash errado é recusado e a versão atual continua intacta.
- Queda de rede no meio do download não quebra nada e a tentativa é refeita depois.
- Depois de atualizar, a versão exibida no app é a nova.
- Web: usuário com formulário aberto não perde o que digitou.
- Logs mostram cada etapa.

## Fora do escopo

- Migração de dados entre versões (tratar separado, mas o updater precisa esperar a migração terminar antes de considerar a atualização concluída).
- Painel de administração de releases.
