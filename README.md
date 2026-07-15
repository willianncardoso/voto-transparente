# Voto Transparente

App aberto e sem back-end para ajudar você e sua comunidade a votar com mais informação. Ele reúne:

- **Como votaram** os deputados federais em cada pauta — dados ao vivo da API de Dados Abertos da Câmara.
- **Buscar & Seguir**: busque parlamentares (por nome/UF) e pautas (por palavra-chave) e adicione a uma lista para acompanhar.
- **Página de consulta** (`consulta.html`): uma visão enxuta e read-only só do que você segue, com placar das votações mais recentes ao vivo. Boa para consultar no dia a dia.
- **Links de verificação** nos portais oficiais (TSE, Portal da Transparência, Senado, Câmara).
- **Meu Voto**: registro de em quem você votou (deste ano ou retroativo), salvo **apenas no seu aparelho**.

Tanto a lista de seguidos quanto os registros de voto ficam no `localStorage` do navegador — nunca saem do aparelho.

## Privacidade e segurança

Não há servidor, banco de dados nem coleta de dados. Tudo roda no navegador:

- Seus registros de voto ficam no `localStorage` do próprio aparelho. Ninguém mais acessa.
- Você pode **exportar** um backup em JSON, **importar** em outro aparelho ou **apagar tudo** a qualquer momento.
- As consultas de votações batem direto nas APIs públicas dos órgãos e **não enviam nenhum dado seu**.

## Rodando localmente

É um único arquivo. Basta abrir `index.html` no navegador (duplo clique). Sem instalação.

## Publicando de graça no GitHub Pages

1. Crie um repositório novo no GitHub (ex.: `voto-transparente`).
2. Suba os arquivos desta pasta:

   ```bash
   cd voto-transparente
   git init
   git add .
   git commit -m "Voto Transparente: primeira versão"
   git branch -M main
   git remote add origin https://github.com/SEU_USUARIO/voto-transparente.git
   git push -u origin main
   ```

3. No GitHub: **Settings → Pages → Branch: `main` / root → Save**.
4. Em ~1 minuto o site fica no ar em `https://SEU_USUARIO.github.io/voto-transparente/`.

## Como editar os dados

Tudo o que você precisa mexer está no topo do `<script>` em `index.html`:

- **`PAUTAS_IMPORTANTES`** — pautas em destaque. Cada item precisa do `id` da votação na Câmara.
- **`CANDIDATOS`** — lista de candidatos por nível (Federal, Estadual, Municipal, Presidencial). Foque nos **nomes** e nos links de verificação.
- **`PORTAIS`** — portais oficiais (já vem preenchido).

### Como achar o ID de uma votação

Abra a aba **"Como votaram (Federal)" → "Buscar votações recentes"**. Cada resultado mostra o `ID` (ex.: `2265603-43`). Copie esse ID para dentro de `PAUTAS_IMPORTANTES`:

```js
const PAUTAS_IMPORTANTES = [
  { id: "2265603-43", titulo: "Nome da pauta", descricao: "Uma linha explicando o que estava em jogo." },
];
```

Você também pode navegar as votações em https://www.camara.leg.br/votacoes.

## Fontes de dados

- Câmara dos Deputados — Dados Abertos: https://dadosabertos.camara.leg.br/
- Senado Federal: https://www25.senado.leg.br/
- TSE — DivulgaCand/DivulgaContas: https://divulgacandcontas.tse.jus.br/
- Portal da Transparência: https://portaltransparencia.gov.br/

## Limitações

- Votações nominais ao vivo cobrem hoje a **Câmara dos Deputados**. Senado, assembleias estaduais e câmaras municipais não têm uma API unificada — por isso esses níveis entram como links e como itens editáveis em `CANDIDATOS`.
- Sempre confira nas fontes oficiais antes de concluir. Este app agrega e facilita; não substitui os portais.

## Licença

MIT — use, adapte e distribua livremente. Veja `LICENSE`.
