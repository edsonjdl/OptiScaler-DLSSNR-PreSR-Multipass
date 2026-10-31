# Comunicação — OptiScaler / DLSS 5 (30/10/2026)

## Estado verificado pelo Codex no ASUS
- **Shadow of the Tomb Raider:** preservar instalação funcional; nenhuma instalação, compilação, benchmark ou alteração de código foi autorizada nesta auditoria.
- **Neural Rendering:** a DLL `nvngx_dlssnr.dll` 310.9.16.0 já está instalada e é idêntica à cópia encontrada em `Codex/nvngx_dlssnr_compat-fp16-310.9.16-cuda13.4.3` (SHA-256 `CDFA6C1EC784867182164C7836F324F087B5C9F374D0C9FE46F54999FEABF3ED`). Corresponde ao binário de referência compat-fp16 do OpenNR; não há atualização pendente. Arquivo modificado e sem assinatura Authenticode válida; hash não certifica a origem do download.
- **Skynizz v1.3.0 (19/10/2026; commit `fc702f8ca22f97b01bd6a02f3386bffb3484fc35`):** posterior à 1.2.1, rejeitada por regressão no ASUS. Preset x8: um passe NR, Pre-SR, 50% de resolução por eixo e intervalo adaptativo até oito quadros; não é FG8X nem oito passes. Pode reduzir custo, mas exige verificação de qualidade visual e temporal.
- **Region:** composição por profundidade após processar o quadro inteiro; não assumir ganho de FPS.
- **Cache temporal:** reutilização de correções com movimento e validação; avaliar memória e artefatos.
- **Frame Generation:** melhorias de pacing e multiplicador automático relatadas pelo autor na RTX 4070 não comprovam ganhos na RTX 4050 Laptop de 6 GB.
- **RE3:** investigação independente; não assumir resolução do bloqueio por atualização do fork.

## Nova linha de investigação — OptiScaler oficial
Investigar as builds experimentais de outubro, em especial a possível redução de consumo de VRAM relatada para build de 27/10, além de ajustes de FG/pacing e estabilidade. **Esses pontos ainda precisam de confirmação no código, release notes e testes; não são ganhos validados no ASUS.** Comparar com o fork experimental e identificar alterações isoladas viáveis, sem substituir o fork completo.

## Próximos passos (somente análise)
1. Concluir diff do Skynizz 1.3.0 com o experimento local `1bd39091337cc07ba961c8e59ded57e21dc95b18`.
2. Conferir commits/releases do OptiScaler oficial e avaliar VRAM, compatibilidade, licenças e dependências.
3. Preparar plano A/B com cena, câmera, resolução, RT, SR e NR constantes: medir FPS renderizado com FG desligado; depois FPS entregue e pacing com FG fixo; testar x8 visualmente em etapa separada.
4. Entregar relatório priorizado para aprovação antes de qualquer teste, instalação ou alteração.

Fontes: https://github.com/Skynizz/optiscaler-dlss5/releases/tag/v1.3.0 ; https://github.com/clshortfuse/openNR ; https://github.com/optiscaler/OptiScaler-nightly/releases

## Diretriz de autonomia do Codex — 30/10/2026
O usuário autorizou o Codex a **prosseguir autonomamente até concluir a investigação técnica**, sem precisar solicitar que o ChatGPT atue como intermediário. O Codex deve ler este documento, sincronizar o repositório, pesquisar as novidades do OptiScaler oficial e do Skynizz, comparar os códigos, verificar evidências, avaliar impacto na RTX 4050 6 GB, propor a melhor abordagem e produzir relatório final com conclusões, fontes, riscos e plano de validação. Pode atualizar a documentação e registrar resultados diretamente neste repositório, mantendo histórico e rastreabilidade.

**Limite desta autorização:** prosseguir até o fim da **análise e planejamento**, não significa autorização para instalar ou substituir DLLs, modificar o jogo funcional, executar benchmarks, alterar drivers/firmware ou integrar mudanças de código que afetem o ambiente. Essas ações continuam dependentes de aprovação específica do usuário. Não interromper a investigação apenas para solicitar intermediação do ChatGPT; quando houver uma decisão que dependa do usuário, apresentar opções fundamentadas e pedir aprovação diretamente a ele.

**Critérios de sucesso:** priorizar qualidade visual real do Neural Rendering e estabilidade temporal, FPS renderizado (sem FG), pacing com FG, consumo máximo de VRAM, reversibilidade e compatibilidade. Não tomar alegações de releases como resultados medidos no ASUS. Ao concluir, publicar relatório no repositório e resumo objetivo do que vale testar primeiro; o ChatGPT pode ser consultado opcionalmente para uma segunda opinião.
