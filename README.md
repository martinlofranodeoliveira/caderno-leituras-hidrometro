# Caderno de Leituras de Hidrômetro

Organize leituras inicial e final e localize medições com ordem invertida ou evidência de leitura pendente.

## Como usar

Abra a [demonstração](https://martinlofranodeoliveira.github.io/caderno-leituras-hidrometro/). Para uso local, execute `python3 -m http.server 8000` no diretório e abra `http://localhost:8000`.

Preencha o identificador, o hidrômetro, as leituras em litros e a confirmação da conferência. Clique em **Conferir leituras** para localizar registros pendentes ou inválidos. Use **Baixar relatório** para levar a tabela em CSV.

## Contrato público

Finalidade: Usar identificadores fictícios de medidores e registrar o mesmo hidrômetro antes e depois de um período, em litros inteiros. Uma troca de medidor inicia outro período.
A entrada é exatamente {rows}, com até 150 linhas. Cada linha é um objeto com id e os campos abaixo. id é string ASCII no padrão ^[A-Za-z0-9][A-Za-z0-9_-]{0,31}$, sem trim. Erro no envelope, linha que não seja objeto ou id inválido retorna exatamente {error:"INVALID_INPUT"}.
Campos na ordem declarada:
- medidor (Referência do medidor): string após trim JS, 1–100 unidades UTF-16, sem controles C0/C1, bidi U+202A–U+202E/U+2066–U+2069 ou substitutos isolados.
- inicialLitros (Leitura inicial em litros): número inteiro de 0 até 1000000, inclusive.
- finalLitros (Leitura final em litros): número inteiro de 0 até 1000000, inclusive.
- fotoConferida (Registro da leitura conferido): booleano literal true ou false.
Condições para linha apta, na ordem declarada:
- sequenciaValida (Leitura final não é menor que a inicial): finalLitros >= inicialLitros.
- leituraConferida (Conferência da leitura registrada): fotoConferida === true.
Retorne {rows,summary}. Preserve a ordem original e o id original. Em cada linha, devolva id, todos os campos declarados (texto normalizado com trim, números com zero positivo, booleano original; null quando ausente ou inválido), status e issues. Na validação, cada campo ausente ou inválido acrescenta seu nome a issues na ordem declarada. Campo além de id e dos campos declarados acrescenta unexpectedFields uma única vez, ao final. Se houver outro id igual após converter ASCII para minúsculas, marque todas as ocorrências como DUPLICATE e use somente duplicateId em issues. Sem duplicata, status INVALID se houver problemas de validação. Avalie as condições somente em linhas sem esses problemas. Use PENDING se alguma falhar e, em issues, as respectivas chaves das condições em sua ordem. Demais linhas usam OK. issues é uma string em que as chaves são unidas por vírgula e espaço, vazia se não houver. summary contém valid, pending, invalid e duplicates, números de linhas OK, PENDING, INVALID e DUPLICATE, respectivamente.

## Escopo

Demonstração offline com exemplos fictícios. A equipe responsável avalia documentação, conformidade e habilitação. A interface processa os dados no navegador. O motor foi gerado com Gemini 4 Neon no Vertex AI.

## Testes

Execute `node scripts/test.mjs public.json`.

## Licença

MIT. Acorn preserva sua licença em scripts/vendor/LICENSE-Acorn.txt.
