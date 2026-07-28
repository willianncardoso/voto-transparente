# Voto Transparente

App aberto e sem back-end para ajudar você e sua comunidade a votar com mais informação. Ele reúne:

- **Como votaram** os deputados federais em cada pauta — dados ao vivo da API de Dados Abertos da Câmara. Do próprio resultado da votação dá para **seguir a pauta** com um clique.
- **Buscar & Seguir**: busque **deputados federais (Câmara) e senadores (Senado)** por nome e UF, e pautas por palavra-chave, e adicione a uma lista para acompanhar.
- **Página de consulta** (`consulta.html`): uma visão enxuta e read-only só do que você segue. As pautas vêm **agrupadas pela situação** (em tramitação, viraram lei, rejeitadas, arquivadas) e cada parlamentar mostra a **data da última votação**, quando disponível.
- **Links de verificação** nos portais oficiais (TSE, Portal da Transparência, Senado, Câmara).
- **Meu Voto**: registro de em quem você votou (deste ano ou retroativo), salvo **apenas no seu aparelho**.
- **Backup único** em JSON com os registros de voto **e** a lista de seguidos, para levar de um aparelho a outro.
- A **aba aberta e os filtros** são lembrados entre visitas.

Tanto a lista de seguidos quanto os registros de voto ficam no `localStorage` do navegador — nunca saem do aparelho.

## Privacidade e segurança

Não há servidor, banco de dados nem coleta de dados. Tudo roda no navegador:

- Seus registros de voto ficam no `localStorage` do próprio aparelho. Ninguém mais acessa.
- Você pode **exportar** um backup em JSON, **importar** em outro aparelho ou **apagar tudo** a qualquer momento.
- As consultas de votações batem direto nas APIs públicas dos órgãos e **não enviam nenhum dado seu**.

### O que fica guardado no navegador

| Chave | Conteúdo | Vai no backup? |
| --- | --- | --- |
| `voto-transparente:meus-votos` | Seus registros de voto | Sim |
| `voto-transparente:seguindo` | Parlamentares e pautas que você segue | Sim |
| `voto-transparente:ui` | Última aba aberta e filtros | Não — é só conveniência de navegação |

### Formato do backup

Um único arquivo (`voto-transparente-backup.json`) com as duas chaves de dados:

```json
{
  "app": "voto-transparente",
  "versao": 1,
  "exportadoEm": "2026-07-28T12:00:00.000Z",
  "voto-transparente:seguindo": { "cand": [], "pauta": [] },
  "voto-transparente:meus-votos": []
}
```

Importar **soma** aos itens já existentes, sem sobrescrever — a deduplicação é por `id`. Backups antigos (que eram só um array de registros de voto) continuam sendo aceitos.

## Rodando localmente

São dois arquivos estáticos. Basta abrir `index.html` no navegador (duplo clique). Sem instalação, sem build.

Se preferir servir por HTTP (mais parecido com o GitHub Pages):

```bash
cd voto-transparente
python3 -m http.server 8000
# abra http://localhost:8000/index.html
```

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

Abra a aba **"Como votaram" → "Buscar votações recentes"**. Cada resultado mostra o `ID` (ex.: `2265603-43`). Copie esse ID para dentro de `PAUTAS_IMPORTANTES`:

```js
const PAUTAS_IMPORTANTES = [
  { id: "2265603-43", titulo: "Nome da pauta", descricao: "Uma linha explicando o que estava em jogo." },
];
```

Você também pode navegar as votações em https://www.camara.leg.br/votacoes.

## Fontes de dados

- Câmara dos Deputados — Dados Abertos: https://dadosabertos.camara.leg.br/
- Senado Federal — Dados Abertos: https://legis.senado.leg.br/dadosabertos/
- TSE — DivulgaCand/DivulgaContas: https://divulgacandcontas.tse.jus.br/
- Portal da Transparência: https://portaltransparencia.gov.br/

As duas APIs legislativas são abertas, sem chave e com CORS liberado, então o navegador consulta direto. Quando uma delas falha, o app tenta de novo uma vez e, persistindo o erro, mostra um aviso com link para a lista oficial — sem derrubar o resto da página.

## Limitações

- **Votações nominais.** Só votações nominais registram o voto de cada parlamentar. Votações simbólicas e de comissão aparecem sem placar individual — o app diz isso explicitamente em vez de mostrar um placar zerado.
- **Câmara × Senado.** A Câmara publica os votos por votação, mas **não** o histórico de votos por parlamentar; por isso a "última votação" de um deputado é deduzida das votações das pautas que você segue. O Senado publica o histórico por senador, então ali a data é consultada direto na fonte.
- **Busca por senadores** cobre os parlamentares **em exercício** (a lista completa é baixada uma vez e filtrada no navegador). O filtro por casa não se aplica à lista local de `CANDIDATOS`, que aparece só quando nenhuma casa está filtrada.
- **Assembleias estaduais e câmaras municipais** não têm API unificada — esses níveis entram como links e como itens editáveis em `CANDIDATOS`.
- **Candidaturas 2026** aparecem no TSE mais perto da eleição.
- Sempre confira nas fontes oficiais antes de concluir. Este app agrega e facilita; não substitui os portais.

## Acessibilidade

O app é feito para funcionar com teclado e leitor de tela: link "pular para o conteúdo", abas com `role="tab"`/`tabpanel` e navegação por setas, rótulo em todo campo de formulário, contorno de foco visível (`:focus-visible`) e contraste conferido contra o mínimo AA do WCAG (4,5:1 para texto normal).

## Licença

MIT — use, adapte e distribua livremente. Veja `LICENSE`.
