# Mobile (Android e iOS)

Existem dois tipos de atualização e eles convivem:

## 1. Atualização pela loja (código nativo)

Obrigatória para qualquer mudança em código nativo, permissões ou SDKs.

- **Android:** use a API oficial de *In-app updates* do Google Play: modo **flexível** para opcionais (baixa em segundo plano) e **imediato** para obrigatórias.
- **iOS:** não há API equivalente. Consulte a versão publicada (manifesto próprio ou a busca da App Store) e, se houver versão maior, mostre um aviso com botão que abre a página do app na App Store. Obrigatória: tela de bloqueio com o mesmo botão.
- O manifesto (`minVersion`) é o que decide o que é obrigatório nos dois sistemas.

## 2. Atualização OTA (over-the-air): só JS e assets

Para correções rápidas de interface e regras **sem passar pela loja**, quando a tecnologia permite:

| Tecnologia | Ferramenta |
|---|---|
| **Expo / React Native** | **EAS Update** (`expo-updates`) |
| **Flutter** | **Shorebird** |
| React Native sem Expo | `expo-updates` também funciona em projetos bare. O **CodePush do App Center foi descontinuado em 2025**: não use em projeto novo |

Regras para OTA:

1. **Só JS, imagens e textos.** Mudança de código nativo exige versão nova na loja; o OTA tem que ser compatível com o binário instalado (use *runtime version* / *channels*).
2. **Respeite as regras das lojas:** OTA para correções e ajustes, não para mudar o propósito do app ou contornar a revisão.
3. **Aplicação:** baixar em segundo plano e aplicar no próximo início do app. Recarregar na hora só se for crítico e o usuário não estiver no meio de uma ação.
4. **Rollback:** se a atualização OTA travar a abertura, voltar para o pacote embutido (o EAS Update e o Shorebird fazem isso).
5. **Canais:** publique primeiro num canal de teste/preview, depois em produção, com liberação gradual.

## Testes rápidos

1. Publique uma mudança de texto via OTA: ao abrir duas vezes o app, o texto novo aparece.
2. Publique uma versão com `minVersion` acima da instalada: aparece a tela de atualização obrigatória.
3. Modo avião durante o download: o app abre normalmente na versão atual.
