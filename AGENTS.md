# Instruções para a IA (ou dev) que abriu este repositório

Se o usuário te passou este repositório, **sua tarefa é implementar atualização automática no projeto dele**, seguindo a [`SPEC.md`](SPEC.md). Fale com o usuário em português do Brasil, a menos que ele use outro idioma.

## Passo 1: entenda o projeto

Antes de escrever código, descubra e **mostre ao usuário** o que encontrou:

- **Plataforma e tecnologia:** procure `package.json` (Electron, Tauri, Vite/Next/React), `src-tauri/`, `*.csproj` / `*.sln` (.NET), `pubspec.yaml` (Flutter), `app.json` / `app.config.*` (Expo), `Podfile`/`build.gradle` (mobile nativo), `Cargo.toml`, `pyproject.toml` + PyInstaller, etc.
- **Onde fica a versão** hoje (e se há mais de um lugar; unifique em um só).
- **Como o projeto é publicado:** GitHub Releases, loja, hospedagem web, CI existente.
- **Se já existe** algum mecanismo de atualização ou verificação de versão.

Se o projeto tiver mais de uma plataforma (ex.: site + app desktop), trate cada uma separadamente.

## Passo 2: escolha o caminho e confirme

Leia o guia da plataforma:

- Web (site, SPA, PWA) → [`plataformas/web.md`](plataformas/web.md)
- Desktop (Electron, Tauri, .NET, MSIX, macOS) → [`plataformas/desktop.md`](plataformas/desktop.md)
- Mobile (Android, iOS, Expo, Flutter) → [`plataformas/mobile.md`](plataformas/mobile.md)

Proponha ao usuário, numa mensagem curta: a ferramenta escolhida e por quê, onde o manifesto e os arquivos vão ficar hospedados, se precisa de certificado/chave de assinatura (e quem vai providenciar), e o que muda para o usuário final. **Espere o OK** antes de instalar dependências ou mexer no CI.

## Passo 3: implemente

1. Unifique a versão em uma única fonte e mostre-a na tela "Sobre".
2. Implemente o fluxo da `SPEC.md`: verificação → decisão (opcional/obrigatória pelo `minVersion`) → download em segundo plano → **verificação de SHA-256 e assinatura** → aplicação → plano B → logs.
3. Crie o manifesto no formato de [`exemplos/version.json`](exemplos/version.json) (ou o formato nativo da ferramenta, como o `latest.yml` do electron-updater, mapeando os mesmos campos).
4. Automatize a publicação no CI (tag `vX.Y.Z` → build → assinatura → manifesto com hash → upload).
5. Adicione a opção de desativar a verificação automática e o botão "Verificar atualizações".

## Passo 4: teste e entregue

- Rode os testes do [`CHECKLIST.md`](CHECKLIST.md) e mostre a evidência de cada item (log, print ou explicação de como testou).
- Entregue: o que foi alterado, como publicar uma nova versão passo a passo, onde ficam os logs e o que o usuário precisa providenciar (certificado, conta na loja, segredo no CI).

## Limites

- **Nunca** coloque chave privada de assinatura, token ou senha no repositório nem dentro do app. Use segredos do CI e explique ao usuário como cadastrar.
- Não troque a forma de publicação ou o CI sem o OK do usuário.
- Não desative verificação de hash/assinatura "para testar".
- Não use o CodePush do App Center (descontinuado).
