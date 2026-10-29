# Automatizando minha pasta Downloads com Python e IA

#Python #IA #Automação

## Introdução

A partir desta publicação, quero compartilhar o que venho aprendendo na minha jornada com Python, IA e automação.

Para começar, vamos falar de uma tarefa que todo mundo conhece e ninguém gosta: **organizar a pasta Downloads**.

Quando parei para observar minha rotina, uma pergunta me chamou a atenção:

**Quanto tempo eu perco por semana procurando e renomeando arquivos?**

Fiz as contas e levei um susto. Eram cerca de 100 minutos por semana, algo perto de 85 horas por ano. Foi aí que decidi automatizar.

## A ideia

Criei um script em Python que:

* Lê o texto das primeiras páginas de cada PDF
* Pede a uma IA um nome claro e uma categoria
* Move o arquivo para a pasta certa

Uma curiosidade: organizar só por extensão resolve pouco. O que realmente atrapalha são os **nomes** (`scan0042.pdf`, `documento (3).pdf`), e para nomear bem é preciso entender o conteúdo. É aí que a IA ajuda.

## O código

```bash
pip install pypdf anthropic
```

```python
import json, re, shutil
from pathlib import Path
from anthropic import Anthropic
from pypdf import PdfReader

DOWNLOADS = Path.home() / "Downloads"
DESTINO = DOWNLOADS / "Organizado"
SIMULAR = True  # True = só mostra o que faria
client = Anthropic()  # usa a variável ANTHROPIC_API_KEY


def ler_pdf(caminho):
    try:
        paginas = PdfReader(caminho).pages[:2]
        return "".join(p.extract_text() or "" for p in paginas)[:3000]
    except Exception:
        return ""


def sugerir(texto):
    prompt = (
        "Responda SOMENTE com JSON no formato "
        '{"nome": "nome-curto-com-hifens", "categoria": "uma-palavra"}. '
        "Categorias: fatura, contrato, comprovante, artigo, outros.\n\n"
        f"Texto:\n{texto}"
    )
    r = client.messages.create(
        model="claude-haiku-5-5",
        max_tokens=100,
        messages=[{"role": "user", "content": prompt}],
    )
    bruto = r.content[0].text.replace("```json", "").replace("```", "")
    return json.loads(bruto.strip())


for pdf in DOWNLOADS.glob("*.pdf"):
    texto = ler_pdf(pdf)
    if not texto.strip():
        continue
    try:
        info = sugerir(texto)
    except Exception as erro:
        print(f"Falha em {pdf.name}: {erro}")
        continue

    nome = re.sub(r"[^a-z0-9\-]", "", info["nome"].lower())[:60] or "documento"
    destino = DESTINO / info["categoria"] / f"{nome}.pdf"
    print(f"{pdf.name} -> {info['categoria']}/{destino.name}")

    if not SIMULAR and not destino.exists():
        destino.parent.mkdir(parents=True, exist_ok=True)
        shutil.move(str(pdf), str(destino))
```

Comece sempre com `SIMULAR = True`. Quando os resultados estiverem bons, mude para `False`.

## E a privacidade?

Os PDFs são enviados a uma API externa, então evite documentos sensíveis, como contratos confidenciais e dados bancários. Esse cuidado também faz parte de automatizar com responsabilidade.

## Resultado

* Antes: ~100 minutos por semana
* Depois: ~2 minutos por semana (só revisão)
* Economia: cerca de **85 horas por ano**

## Próximos passos

Meu aprendizado tem sido construído na prática, testando, errando e ajustando. E essa jornada continua!

Na próxima publicação, quero evoluir o projeto: adicionar OCR para PDFs escaneados e criar uma interface simples para aprovar cada sugestão antes de mover o arquivo.

E você, qual tarefa chata gostaria de automatizar? Conta nos comentários!

Até a próxima!
