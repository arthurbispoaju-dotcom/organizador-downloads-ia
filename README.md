# organizador-downloads-ia
Script em Python que organiza a pasta Downloads usando IA para nomear e categorizar PDFs automaticamente.



## O que faz

1. Lê o texto das duas primeiras páginas de cada PDF
2. Pede a uma IA um nome claro e uma categoria
3. Renomeia e move o arquivo para `Organizado/<categoria>/`

## Como usar

```bash
git clone https://github.com/SEU-USUARIO/organizador-downloads-ia.git
cd organizador-downloads-ia
pip install -r requirements.txt
```

Defina a chave de API como variável de ambiente:

```bash
# Linux/Mac
export ANTHROPIC_API_KEY="sua-chave-aqui"

# Windows (PowerShell)
setx ANTHROPIC_API_KEY "sua-chave-aqui"
```

Rode o script:

```bash
python organizador.py
```

Por padrão, `SIMULAR = True`: o script só mostra o que faria. Depois de conferir, mude para `False` no código.

## Privacidade

O texto dos PDFs é enviado a uma API externa. Evite usar com documentos sensíveis.

## Artigo

Escrevi sobre o projeto na DIO: [link do artigo]

## Licença

MIT
