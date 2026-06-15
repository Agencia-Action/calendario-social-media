# Contexto Action+ — Social Media & Calendário de Publicações

> **Documento de sincronização entre instâncias do Claude Code.**
> Use este arquivo para alinhar o sistema da agência (Supabase + GitHub) com a LP do calendário de publicações. Contém o padrão de marca, os clientes, os links e a estrutura de dados atual.
> **Última atualização:** 15/06/2026

---

## 1. Identificação

- **Agência:** Action+ (assessoria de crescimento empresarial — estratégia, branding, tráfego pago, social media).
- **Site:** agenciaaction.com
- **E-mail:** ads@agenciaaction.com
- **Conta GitHub:** `Agencia-Action`

---

## 2. Projeto: LP Calendário de Publicações

Landing page estática (HTML único, sem build) que exibe o calendário de posts de social media. Ao clicar em uma publicação, abre o Instagram do cliente; nos cards inferiores, abre o Drive do criativo e copia a legenda.

| Item | Valor |
|------|-------|
| Arquivo fonte | `Desktop/Instagram/calendario-social-media/index.html` |
| Repositório | https://github.com/Agencia-Action/calendario-social-media |
| URL pública (GitHub Pages) | https://agencia-action.github.io/calendario-social-media/ |
| Hospedagem | GitHub Pages — branch `main`, raiz `/` |
| Deploy | `git add . && git commit -m "..." && git push` → atualiza em ~1 min |

Os dados ficam em duas arrays JavaScript dentro do `<script>` do `index.html`: **`clients`** e **`posts`** (ver seção 5).

---

## 3. Padrão visual (Brandbook Action+ v1.0 / Out 2025)

### Cores oficiais (HEX)
| Nome | HEX | Uso |
|------|-----|-----|
| Action Green | `#91FF56` | Destaque principal, CTAs, "hoje" |
| Strategy Green | `#4DC149` | Confirmações, apoio |
| Dark Blue | `#042D46` | Superfícies |
| Very Dark Blue | `#0C2335` | Cartões |
| Almost Black | `#00121D` | Fundo da página |
| Light Blue | `#A3C6D5` | Secundário |
| Very Light Blue | `#D4EBF9` | Detalhes |
| White | `#FFFFFF` | Texto sobre fundo escuro |

### Tipografia
- **Clash Display SemiBold** — exclusiva do logotipo ("Action+"). Não usar em texto corrido.
- **Inter** — corpo de texto e títulos.
- Estética **flat, limpa, funcional**. **A marca não usa gradiente.**

---

## 4. Clientes ativos

| Cliente | Instagram | Cor na LP |
|---------|-----------|-----------|
| Academia Hype | https://www.instagram.com/academia_hype/ | `#91FF56` |
| Porcelana Schmidt | https://www.instagram.com/porcelanaschmidt/ | `#A3C6D5` |
| Federação Paranaense de Futsal | https://www.instagram.com/federacaoparanaensedefutsal/ | `#2E6E9E` |

### Drives de criativos (Google Drive)
| Criativo | Cliente | Link |
|----------|---------|------|
| Alongamento ("O que acontece quando você se alonga") | Hype | https://drive.google.com/drive/folders/1MZ0UFFpSAlG9STdLvhD0a6NnboSt0W5-?usp=sharing |
| Coleção Esfera ("Cada detalhe, pensado em você") | Schmidt | https://drive.google.com/drive/folders/1CfwgTlBLZXEle7y3n7b1ok8vrtG_rrIK |
| Coleção Guaporé ("Utilidade e beleza") | Schmidt | https://drive.google.com/drive/folders/1XcePwBhG95UahKuLUielZjTmSvhlK0qe |

---

## 5. Calendário de publicações (estado atual — 15/06/2026)

| Data | Hora | Cliente | Título | Formato | Status | Drive |
|------|------|---------|--------|---------|--------|-------|
| 08/06/2026 | 18:30 | Porcelana Schmidt | Textura que permanece | Estático | Publicado | — |
| 09/06/2026 | 14:50 | Fed. Futsal | Memórias | Carrossel | Publicado | — |
| 09/06/2026 | 18:00 | Fed. Futsal | Taça Brasil Sub-17 — Encerramento | Estático | Agendado | — |
| 10/06/2026 | 12:00 | Porcelana Schmidt | O branco que aquece — Noiva | Estático | Agendado | — |
| 11/06/2026 | 18:30 | Porcelana Schmidt | Como montar a mesa do encontro | Carrossel | Agendado | — |
| 15/06/2026 | 10:55 | Academia Hype | O que acontece com seu corpo quando você se alonga? | Estático | Agendado | ✅ |
| 15/06/2026 | 12:00 | Porcelana Schmidt | Ambientação Inverno | Reels | Agendado | — |
| 15/06/2026 | 12:00 | Porcelana Schmidt | Cada detalhe, pensado em você — Coleção Esfera | Reels | Agendado | ✅ |
| 16/06/2026 | 12:00 | Porcelana Schmidt | Coleção Guaporé — Utilidade e beleza | Carrossel | Agendado | ✅ |
| 17/06/2026 | 16:20 | Academia Hype | Temperatura baixando e agora...? Como manter a frequência de treinos no inverno | Carrossel | Agendado | — |

---

## 6. Estrutura de dados (contrato para integração Supabase)

A LP consome dois objetos. Para sincronizar com o Supabase, sugere-se o schema abaixo (o front pode passar a ler de uma API/JSON exportado do Supabase em vez das arrays hard-coded).

### Tabela `clients`
| Coluna | Tipo | Exemplo |
|--------|------|---------|
| id | text (slug) | `hype` |
| name | text | `Academia Hype` |
| handle | text | `@academia_hype` |
| instagram_url | text | `https://www.instagram.com/academia_hype/` |
| color | text (hex) | `#91FF56` |
| text_color | text (hex) | `#00121D` |

### Tabela `posts`
| Coluna | Tipo | Exemplo |
|--------|------|---------|
| id | uuid | — |
| client_id | fk → clients.id | `hype` |
| date | date | `2026-06-15` |
| time | time | `10:55` |
| title | text | `O que acontece com seu corpo quando você se alonga?` |
| format | enum | `Estático` \| `Carrossel` \| `Reels` |
| status | enum | `Agendado` \| `Publicado` |
| drive_url | text (nullable) | link da pasta no Drive |
| copy | text (nullable) | legenda completa do post |

### Formato JSON que a LP espera
```json
{
  "clients": {
    "hype": { "name": "Academia Hype", "handle": "@academia_hype", "url": "...", "color": "#91FF56", "text": "#00121D" }
  },
  "posts": [
    { "date": "2026-06-15", "time": "10:55", "client": "hype", "title": "...", "format": "Estático", "status": "Agendado", "drive": "...", "copy": "..." }
  ]
}
```

---

## 7. Integração Supabase + GitHub (pendências para o outro Claude Code)

- [ ] Criar as tabelas `clients` e `posts` no Supabase conforme seção 6.
- [ ] Migrar os dados atuais (seção 5) para o Supabase.
- [ ] Expor um endpoint/JSON (Supabase REST ou função) que devolva o formato da seção 6.
- [ ] Adaptar o `index.html` para buscar os dados desse endpoint (substituir as arrays hard-coded por `fetch`).
- [ ] (Opcional) Automação de publicação no Instagram via Meta API + tarefa agendada, lendo `posts` com `status = Agendado` e `date/time` chegando.

> **Nota:** A API oficial do Instagram publica na hora; o "agendamento" real é feito por um job (cron) que dispara no horário de cada post.
