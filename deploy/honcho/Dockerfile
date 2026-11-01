FROM ghcr.io/plastic-labs/honcho:v3.5.0@sha256:48c08d96b96d9a8012e6200bd9aa1dd070a96fb1c417fe37cd2ca40650c809e1

# Cache tokenizers during the image build so runtime remains internal-only.
RUN /app/.venv/bin/python -c "import tiktoken; tiktoken.get_encoding('o200k_base'); tiktoken.get_encoding('cl100k_base')"
