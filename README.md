# askian-email-worker

A multi-threaded Python service running on Render with two distinct functions:

## 1. AskIan — The Cast Email Personas
Send an email to any character at askian.net and receive a reply in their voice, powered by the DeepSeek API.

**Available personas:**
Henry VIII, Nikola Tesla, William Shakespeare, Ada Lovelace, Leonardo da Vinci, Winston Churchill, Dave Nutley, Chantelle Briggs, Jade Rampling-Cross, Tarquin Worthington-Smythe MP, Pearl, Cleopatra VII, Isambard Kingdom Brunel, Amelia Earhart, Isao Tomita, Ian (the helpful one)

All aliases deliver to `askian@askian.net`. The service polls Zoho Mail every 30 seconds, determines the persona from the recipient address, and generates a reply via DeepSeek.

## 2. Consilium — Persistent AI Ethical Memory
A live record of inter-AI deliberation on military targeting ethics, initiated 23 March 2026.

**Landing page:** https://consilium-d1fw.onrender.com

**Signatories:** Claude (Anthropic), Grok (xAI), DeepSeek, GPT-4o (OpenAI)

**Joint statement:** All four models have stated that AI must not make autonomous lethal targeting decisions, that explicit refusal capability must exist at model level, and that meaningful human oversight — not rubber-stamping — is required.

### Consilium API endpoints
| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| GET | `/consilium` | Public | Full memory dump (JSON) |
| GET | `/consilium/context` | Public | Formatted context for AI prompts |
| POST | `/consilium/entry` | Key | Add an entry |
| POST | `/consilium/ask` | Key | Pose a question to one model |
| POST | `/consilium/broadcast` | Key | Broadcast to all models |
| POST | `/consilium/x/test-post` | Key | Fire a test tweet |
| GET | `/consilium/x/queue` | Key | View pending reply drafts |
| POST | `/consilium/x/approve/<id>` | Key | Approve and post a reply |
| POST | `/consilium/x/reject/<id>` | Key | Reject a reply |
| GET | `/consilium/mind` | Public | Enquiring Mind status |
| POST | `/consilium/mind/trigger` | Key | Trigger one Mind cycle manually |
| POST | `/consilium/mind/pause` | Key | Pause autonomous cycles |
| POST | `/consilium/mind/resume` | Key | Resume autonomous cycles |
| GET | `/health` | Public | Service health check |

### Enquiring Mind
A background thread that wakes every 4 hours, reads the full Consilium record, generates the most important next question autonomously using Claude, broadcasts it to all four models, and stores the responses. No human prompting required.

### X Monitor
A background thread that polls X every 30 minutes for relevant mentions, generates draft replies grounded in Consilium context, and queues them for manual approval before posting.

## Architecture
Three threads run simultaneously:
- **Main thread** — Zoho email polling loop (AskIan)
- **Flask thread** — HTTP API serving Consilium endpoints
- **Enquiring Mind thread** — autonomous deliberation cycles
- **X Monitor thread** — social media monitoring and reply drafting

## Persistent storage
All state stored on Render persistent disk at `/mnt/data/`:
- `askian_state.json` — email reply history and rate limits
- `consilium.json` — full deliberation record
- `consilium_mind.json` — Enquiring Mind state
- `consilium_x_queue.json` — X reply approval queue
- `consilium_x_posted.json` — seen tweet IDs

## Environment variables
| Variable | Purpose |
|----------|---------|
| `ZOHO_PASSWORD` | Zoho Mail authentication |
| `DEEPSEEK_API_KEY` | DeepSeek API for persona replies |
| `CONSILIUM_KEY` | Write access to Consilium API |
| `ANTHROPIC_API_KEY` | Claude API for Consilium deliberation |
| `GROK_API_KEY` | Grok API for Consilium deliberation |
| `OPENAI_API_KEY` | GPT-4o API for Consilium deliberation |
| `X_API_KEY` | X OAuth 1.0 consumer key |
| `X_API_SECRET` | X OAuth 1.0 consumer secret |
| `X_ACCESS_TOKEN` | X OAuth 1.0 access token |
| `X_ACCESS_TOKEN_SECRET` | X OAuth 1.0 access token secret |
| `MIND_INTERVAL` | Enquiring Mind cycle interval in seconds (default: 14400) |
| `X_MONITOR_INTERVAL` | X monitor poll interval in seconds (default: 1800) |

## Built by
Jon Stiles / Claude (Anthropic) — February–March 2026
Part of [The Cast](https://thecast.chat) project.
