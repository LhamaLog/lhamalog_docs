# Validação da entrega

Conferência em 01/11/2026.

- Os 209 arquivos iniciais mantêm os mesmos SHA-256 da leitura inicial. O SVG recebido depois também foi preservado e incluído no manifesto de 210 originais.
- Os 60 DOCX e 2 XLSX passaram na verificação de integridade dos contêineres ZIP e leitura de XML. O conteúdo textual foi extraído; as 98 e 93 abas das planilhas foram lidas com resolução dos textos compartilhados. Não foi feita revisão visual página a página no Word/Excel.
- As 141 imagens PNG/JPG originais foram completamente decodificadas e examinadas em folhas de contato. As 76 imagens incorporadas na planilha de classes também foram extraídas para análise temporária e examinadas; não alteram a planilha.
- O SVG final foi analisado como XML e renderizado em PNG de 1800 × 2836 para inspeção visual. As quatro exportações PNG anteriores também abriram e foram decodificadas; seus hashes constam do manifesto de exportações.
- Os HTML originais foram lidos como documentos de protótipo; sua presença não foi tomada como aplicação final. Referências locais desses arquivos foram conferidas sem modificar os originais.
- As 24 cópias textuais selecionadas dos repositórios preservam os bytes das fontes baixadas e têm hashes e links para commits exatos no manifesto de proveniência.
- O ZIP de origem passou na conferência de integridade; seus 61 membros já extraídos são idênticos aos originais locais. O vídeo foi mantido intacto e excluído da leitura/revisão por orientação do usuário.
- Os links relativos dos documentos Markdown criados foram resolvidos contra o sistema de arquivos, incluindo acentos e espaços. Links remotos e âncoras internas de protótipos não são tratados como arquivos locais.

## Limites

A validação confirma integridade, leitura e disponibilidade local dos artefatos. Não certifica correção funcional da aplicação. Não foram executados builds, testes de software, containers ou integrações. Acessibilidade dos repositórios privados para o professor depende das permissões dele, não da conta usada nesta consulta. Nenhum commit, push ou publicação foi realizado.

O relatório automático com contagens e resultados está em [validacao-resultados.json](validacao-resultados.json). Os manifests dos [originais](inventario-original.json), das [exportações Figma](figma/exportacoes.json) e das [fontes de código](fontes-repositorios/manifesto.json) permitem rastrear os arquivos.
