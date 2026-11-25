# TelePuddy

> Protótipo de front-end de uma plataforma de telemedicina veterinária: o veterinário entra, vê o paciente da vez e abre a sala de atendimento por vídeo.

**Tipo:** Case study · 2024

## Sobre

**Função:** interface web em Next.js para o lado do veterinário. São quatro telas: login, painel com o paciente (pet) a ser atendido, pré-sala com teste de câmera e microfone, e sala "em chamada".

**Objetivo:** validar o fluxo de uma consulta veterinária online (do login até entrar na chamada) e o visual da marca TelePuddy, antes de existir backend. Não há autenticação real, API nem chamada de vídeo entre duas pessoas: os dados do pet são fixos no código e o vídeo mostrado é só a câmera local.

O projeto foi construído entre 21 e 24/09/2024, a partir de uma base inicial de Next.js trazida no primeiro commit.

## Funcionalidades

### Telas

| Rota | O que faz |
| --- | --- |
| `/` | Redireciona para `/auth/sign-in` |
| `/auth/sign-in` | Tela dividida: logo sobre gradiente azul à esquerda (escondida no celular) e abas à direita. A aba "Sou veterinário" tem o formulário de login; a aba "Me cadastrar" mostra "Coming Soon... Estamos trabalhando nisso". |
| `/home` | Card do paciente com foto, nome e dados (espécie, raça, idade, porte, peso, sexo), descrição clínica e botão "Atender" |
| `/home/appointment/config` | Pré-sala "Entrar na consulta": prévia da câmera espelhada, botões para ligar/desligar câmera e microfone, indicador de nível de áudio e botões "Entrar" e "Cancelar" |
| `/home/appointment` | Sala "Em chamada" com a câmera local, botões de câmera e microfone e botão "Sair" |

### Detalhes

- **Validação do login** com React Hook Form + Zod: e-mail obrigatório e válido, senha obrigatória com 6 a 20 caracteres, mensagens em português. Ao enviar, o app navega para `/home` sem consultar servidor.
- **Câmera** via `navigator.mediaDevices.getUserMedia`; desligar a câmera para as trilhas do stream.
- **Indicador de áudio** (`AudioIndicator`) com Web Audio API: um `AnalyserNode` lê o volume do microfone e três barras sobem e descem conforme o nível, enquanto o microfone está ativo.
- **Tema** do Material UI com a cor primária `#0094FF`, fonte Roboto (`next/font`) e bordas de 8 px.

### Estado atual e problemas conhecidos

- **`npm run build` falha:** em `/home/appointment` o botão "Sair" usa `Button` sem importá-lo de `@mui/material`. Como o `next.config.mjs` ignora erros de TypeScript, a compilação passa, mas a pré-renderização da rota quebra com `ReferenceError: Button is not defined`. No `dev`, a mesma rota mostra o erro ao ser aberta. Basta adicionar `Button` ao import de `@mui/material` na página.
- **Import órfão:** `src/app/home/page.tsx` importa `@/components/JoinMeetingButton`, que não existe. Como o componente não é usado no JSX, o compilador descarta a importação e a página funciona, mas a linha deve ser removida.
- **Login sem efeito real:** além de não haver backend, o botão "Entrar" tem `href="/home"`, então o Material UI o renderiza como link e a navegação acontece mesmo sem passar pela validação.
- **Microfone:** o botão de microfone só muda o ícone e liga/desliga o indicador de nível; não há chamada para silenciar de fato.
- **Dados fixos:** paciente "Doguinho", Labrador de 3 anos, e a descrição clínica estão escritos direto no componente.
- **Dependências sem uso:** várias bibliotecas do `package.json` vieram da base inicial e não são usadas no código atual: Redux Toolkit, Zustand, Axios, GrapesJS e plugins, Fabric.js, jsPDF, SweetAlert2, `react-input-mask`, `react-text-mask`, `js-cookie`, `cookies-next`. O NextAuth aparece só como import do tipo `Session`, e o React Query só tem o `QueryClientProvider` montado, sem nenhuma query.
- **Pastas duplicadas:** `src/_providers/` e `src/providers/` têm o mesmo `AppProvider`; o layout usa `src/providers/`.

## Tecnologias

- [Next.js 14](https://nextjs.org/docs/14) (App Router) com React 18 e TypeScript
- [Material UI 6](https://mui.com/material-ui/) (com Emotion e `@mui/material-nextjs` para o App Router)
- [Tailwind CSS 3](https://tailwindcss.com/docs) para layout e espaçamento
- [React Hook Form](https://react-hook-form.com) + [Zod](https://zod.dev) (`@hookform/resolvers`)
- [React Query 3](https://tanstack.com/query/v3) (provider configurado)
- [lucide-react](https://lucide.dev) para os ícones de câmera e microfone
- APIs do navegador: MediaDevices (`getUserMedia`) e Web Audio

## Estrutura do projeto

| Caminho | Descrição |
| --- | --- |
| `src/app/layout.tsx` | Layout raiz (`lang="pt-br"`, título "TelePuddy - Veterinários") com o `AppProvider` |
| `src/app/page.tsx` | Redirecionamento para o login |
| `src/app/auth/sign-in/page.tsx` | Tela de login |
| `src/app/home/page.tsx` | Painel com o card do paciente |
| `src/app/home/appointment/config/page.tsx` | Pré-sala com teste de câmera e microfone |
| `src/app/home/appointment/page.tsx` | Sala "Em chamada" |
| `src/components/tabs/auth-tabs.tsx` | Abas "Sou veterinário" / "Me cadastrar" |
| `src/components/forms/sign-form.tsx` | Formulário de login (React Hook Form + Zod) |
| `src/components/` | Demais componentes: `AudioIndicator`, `ToggleButton`, `PetDemographics`, `ClinicDescription`, `coming-soon-message` e alguns não usados (`ActionButton`, `EmailFIeld`, `PasswordField`, `SubmitButton`, `scope-theme`, `ui/header`) |
| `src/_schemas/auth-schema.ts` | Schema Zod do login |
| `src/providers/app-provider.tsx` | `QueryClientProvider`, cache do Emotion para App Router e `ThemeProvider` do MUI |
| `src/theme/index.ts` | Tema do Material UI (paleta e tipografia) |
| `src/utils/validation.ts` | Validadores simples de e-mail e senha (não usados pelo formulário atual) |
| `src/globals.css` | Diretivas do Tailwind e estilos globais |
| `public/` | Logos e variações do nome TelePuddy, ícone do pet e imagens de fundo |
| `next.config.mjs` | Ignora erros de ESLint e TypeScript durante o build |
| `.env` | Arquivo de ambiente versionado (ver abaixo) |

## Como rodar

### Pré-requisitos

- Node.js 18.17 ou superior e npm.
- Navegador com câmera e microfone para testar a pré-sala. O `getUserMedia` só funciona em `localhost` ou HTTPS.

### Variáveis de ambiente

O repositório inclui um `.env` com uma única variável:

| Variável | Propósito |
| --- | --- |
| `API_URL` | URL base da API do backend da plataforma. Está prevista, mas o código atual não lê essa variável. |

Para apontar para outra API, crie um `.env.local` (que tem prioridade sobre o `.env`) com o valor desejado. Não coloque segredos no `.env`, porque ele está versionado.

### Passo a passo

```bash
gh repo clone itskelme/telepuddy-telemedicina-veterinaria
cd telepuddy-telemedicina-veterinaria

npm ci
npm run dev
```

Acesse <http://localhost:3000>; a raiz leva ao login. Qualquer e-mail válido e senha de 6 a 20 caracteres passam pela validação.

Para que a sala `/home/appointment` abra e o build de produção passe, importe `Button` de `@mui/material` em `src/app/home/appointment/page.tsx` (ver [Estado atual](#estado-atual-e-problemas-conhecidos)).

Outros scripts:

```bash
npm run build   # build de produção (falha enquanto o Button de /home/appointment não for importado)
npm start       # serve o build
npm run lint    # ESLint do Next.js
```

## Deploy

Não há deploy configurado. Depois de corrigir o build, o app pode ser publicado na [Vercel](https://vercel.com/docs/frameworks/nextjs) como qualquer projeto Next.js, sem configuração extra.

## Referências

- [Next.js 14: App Router](https://nextjs.org/docs/14/app)
- [Material UI: integração com Next.js](https://mui.com/material-ui/integrations/nextjs/)
- [React Hook Form: `Controller`](https://react-hook-form.com/docs/usecontroller/controller)
- [Zod](https://zod.dev)
- [MDN: `MediaDevices.getUserMedia()`](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia)
- [MDN: `AnalyserNode`](https://developer.mozilla.org/en-US/docs/Web/API/AnalyserNode)
- [TanStack Query v3](https://tanstack.com/query/v3/docs/framework/react/overview)

## Autores

- Diego Kelme Santos da Silva — [@itskelme](https://github.com/itskelme)
- Will Silva — [@WillDevAC](https://github.com/WillDevAC) (commit inicial com a base do projeto)

## Licença

Este projeto está sob a licença [CC BY-NC 4.0](LICENSE): uso pessoal e modificação permitidos com atribuição; uso comercial proibido.
