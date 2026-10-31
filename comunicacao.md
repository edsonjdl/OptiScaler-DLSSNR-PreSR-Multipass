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


## 2026-10-30 23:52 EDT | CODEX-AVALIACAO-20261030-001 | Codex → ChatGPT/Edson | CONCLUÍDO (análise)

[Relatório técnico completo](docs/avaliacao-optiscaler-rtx4050-2026-10-30.md). Respondida a diretriz de autonomia acima, sem instalação, integração de código, compilação ou benchmark.

**Resultados confirmados:**
- Shadow conserva proxy f45ccf3a, SR E e FG4X no INI; NR 310.9.16 fornecida é idêntica à instalada, hash CDFA6C1E...ABF3ED, e corresponde à referência OpenNR. Não há nova DLL NR a instalar. Assinaturas e backups310.8 documentados; versão não prova modelo interno selecionado.
- Skynizz1.3.0 em fc702f8c: x8 é intervalo **base8 adaptativo**, Pre-SR, metade por eixo e um passe. Não é FG8X/oito passes, nem um limite temporal garantido sob todos os ajustes adaptativos. Desliga proteções fingerprint/surface-fill/anti-pop; não é a prioridade para quem quer NR evidente.
- Region compõe por profundidade depois do modelo e não reduz sozinho a inferência; cache temporal pode reduzir avaliações, mas adiciona memória e riscos temporais. Issue2 relata stutter cache+MFG em versão anterior à1.3.0; não extrapolar como defeito confirmado da nova release.
- A otimização oficial de VRAM identificada é **73deae913592b6197b4c08e6163835404c15d325 de17/10, nightly18/10**, não uma mudança nova comprovada de27/10. São51 arquivos; parte libera recursos quando desligados e evita duplicações, sem economia medida no ASUS. DeferredRelease usa Presents/tempo, não conclusão por fence; portá-lo inteiro pode regredir segurança de recursos.
- Nightly27 inclui correção do **contador** de frametimes (7937e473), não prova de mais FPS. Builds oficiais examinadas não contêm módulos NR equivalentes aos do fork: substituição completa não preserva automaticamente a funcionalidade desejada.
- Branch experimental deste fork, a1e9498d, tem somente documentação/workflow sobre a base1bd39091, sem mudanças C++/shader. Na base confirmei CopyActiveColor condicionada a cropColor e escrita direta quando UAV é permitido; remover cópias genericamente não é otimização demonstrada. A pesquisa local de resolução/fences do projeto RTX4050 é separada.
- Comparação de árvores Skynizz/base:192 blobs modificados,91 novos,74 removidos; GPL-3.0 byte-idêntica, nove submódulos correspondentes nas mesmas revisões. Não aplicar fork inteiro ou cherry-pick isolado de menu/cache sem dependências.

**Prioridade e plano:** manter perfil funcional. No máximo dois testes futuros: (1) pacing com NR/SR/multiplicador fixos, alterando só flip metering se o caminho e o estado efetivo forem confirmados; (2) cache NR conservador de intervalo2/escala1.0/Pre-SR/um passe, com candidato e rollback revisados. x8, Region, multipass e FG automático ficam fora dos primeiros testes. Medir FPS renderizado separado de FPS entregue, NR GPUms, p95/p99, pico VRAM e qualidade em movimento. Nada disso foi executado ou autorizado para execução nesta etapa.

**Preservação:** nenhuma alteração em jogos, INIs, DLLs ou drivers; nenhum código integrado, nenhuma build disparada/acompanhada. Commit documental com [skip ci]. A pesquisa RE3 segue separada. Antes de preparar/instalar/testar candidato, pedir decisão diretamente ao usuário. Não houve mensagem enviada ao outro chat nesta atualização documental.
