# Checklist de testes do auto-updater

Copie para o PR ou para a issue e marque cada item com evidência (print, vídeo ou trecho de log).

### Detecção
- [ ] Servidor com versão **maior** → oferece a atualização
- [ ] Servidor com versão **igual** → não faz nada
- [ ] Servidor com versão **menor** → não faz nada (sem downgrade)
- [ ] Versão instalada abaixo de `minVersion` → atualização obrigatória, com tela explicando
- [ ] Manifesto inválido ou fora do ar → app segue normal e registra o erro

### Download
- [ ] Interface continua responsiva durante o download
- [ ] Rede cai no meio do download → nada quebra, versão atual intacta, nova tentativa depois
- [ ] Disco cheio → erro registrado, versão atual intacta

### Integridade
- [ ] Arquivo com SHA-256 diferente do manifesto → recusado, não instala
- [ ] (Desktop) Arquivo sem assinatura válida → recusado, não instala

### Aplicação
- [ ] Desktop: não reinicia sozinho com trabalho não salvo
- [ ] Web: usuário com formulário aberto não perde o que digitou
- [ ] Web: depois de atualizar, nenhum arquivo antigo fica preso no cache (Service Worker atualizado)
- [ ] Depois de atualizar, a versão exibida no app é a nova
- [ ] (Se suportado) Falha ao abrir a nova versão → volta para a anterior

### Operação
- [ ] Logs registram verificação, download, hash, sucesso/erro, versão antiga e nova
- [ ] Opção de desativar a verificação automática funciona
- [ ] "Verificar atualizações" manual funciona
- [ ] `rollout` em 0% não entrega a ninguém; em 100% entrega a todos
