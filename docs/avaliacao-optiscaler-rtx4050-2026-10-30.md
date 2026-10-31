# Avaliação OptiScaler / DLSS NR — ASUS RTX 4050 Laptop 6 GB

Data: 30/10/2026, 23:52 EDT (31/10 03:52 UTC). Escopo: análise estática, leitura de arquivos locais e fontes primárias. Nenhum jogo, benchmark, build, instalador ou DLL candidata foi executado nesta tarefa. Não houve alteração na instalação do Shadow nem no experimento de código.

## Conclusão

Manter o Shadow funcional. A NR 310.9.16 já está instalada. Skynizz 1.3.0 contém novidades reais, mas não justifica substituir o conjunto atual integralmente nem importar o fork em bloco. A alegação de VRAM atribuída a 27/10 remonta a uma mudança de 17/10 distribuída na nightly de 18/10; não encontrei nova redução de VRAM específica de 27/10 nos commits/changelog examinados. Não existe porcentagem de economia comprovada no ASUS.

Prioridade: compreender e medir pacing mantendo NR, SR e multiplicador constantes; depois, se necessário e autorizado, avaliar cache NR moderado. x8 e Region não são as primeiras opções para a preferência atual por efeito NR evidente.

## Estado local efetivamente lido

| Componente | Evidência |
|---|---|
| Proxy Shadow | 26.513.920 bytes; build `10.0.0-dev (f45ccf3a) (20260923_133358)`; SHA256 `47C1708C97CC0ECD7E380E15B789740D30E183AAB1D67A2B16B11E43C475B37A` |
| INI Shadow | SHA256 `A5D80F81BA33DD493F6D6261D3679C19BC1290F84F75CB10F59C000D1F7C3094` |
| Configuração salva | FG Enabled=true, input Upscaler, output DLSSG; DLSSG InterpolationCount=3 (4X); SR override=true, preset5 (E); NR Enabled=true, RunBeforeSR=true, Passes/WorkingScale/Preset=auto |
| NR atual e cópia fornecida | 310.9.16.0; 276.162.560 bytes; SHA256 `CDFA6C1EC784867182164C7836F324F087B5C9F374D0C9FE46F54999FEABF3ED`; idênticas |
| NR anterior preservada | 310.8.0.0; 165.840.496 bytes; SHA256 `E67DEE209320CDAFE0E93E45675D7AA34323A53ACC57A72B2E40A181581C989A` |

Os dois hashes NR constam das referências do [OpenNR](https://github.com/clshortfuse/openNR). A atual retorna Authenticode NotSigned; a antiga HashMismatch com certificado NVIDIA presente, portanto nenhuma deve ser descrita como assinatura NVIDIA válida. Correspondência ao hash público não certifica a cadeia do download no Discord. Nenhum dos arquivos foi carregado ou redistribuído nesta análise. A versão da DLL não prova qual variante/modelo interno efetivamente executou.

O INI não confirma por si só o modo DLSS Performance do menu do jogo nem que os valores auto equivalem aos parâmetros efetivos da GPU. O histórico do usuário descreve 4K/Performance/RT Ultra e preferência por NR visível; uma nova medição precisa confirmar essas condições. Não confundir o preset E de SR com preset C de FG ou preset x8 de NR.

## Skynizz: release e implementação

[1.3.0, 19/10/2026](https://github.com/Skynizz/optiscaler-dlss5/releases/tag/v1.3.0), fonte fixada em `fc702f8ca22f97b01bd6a02f3386bffb3484fc35`. Não foi localizada release posterior no canal consultado em 30/10. A 1.2.1 já regrediu no teste anterior deste ASUS; isso não condena automaticamente a 1.3.0, mas impede presumir benefício.

| Recurso | O código faz | Benefício e limite para este ASUS |
|---|---|---|
| x8 NR | Um passe; Pre-SR; WorkingScale=0.5 por eixo; intervalo base8 adaptativo. Desliga fingerprint, surface fill e anti-pop; reinicia históricos após mudança. | Em entrada nominal1080p, NR pode ir a960x540. Reduz trabalho por avaliação e frequência, sacrificando detalhe/atualidade temporal. Não é FG8X, oito passes ou oito vezes mais FPS. Não priorizar. |
| Cache temporal | Guarda correção logarítmica de cor/iluminação, reprojetada por movimento e validada por profundidade/cor; novas texturas/históricos e recursos de rejeição. | Pode reduzir inferências; custa VRAM e pode produzir rastros/pulsação. Não equivale a simplesmente congelar o quadro; também não garante que correção antiga pertença à imagem nova. |
| Region | Shader compõe original e resultado NR por profundidade; o modelo ainda vê o quadro todo. | Pode preservar rostos/cenário. Não reduz por si só a inferência nem identifica semanticamente personagens. Riscos em chão, silhuetas, cabelos e objetos próximos. Recurso de qualidade, não promessa de FPS. |
| FG pacing | Controle NVIDIA flip metering; medidor de quadros entregues/empilhados/atrasados; correções de injeção em jogos sem Streamline. | Relevante à fluidez, mas resultados do autor usam RTX4070/1440p e monitor360Hz G-SYNC. Não comprovam benefício no Shadow/4050. Medidor de entrega no software não é medida física de latência do monitor. |
| Auto multiplier | FGMeter calcula alvo configurado ou95% da frequência, limita ao permitido e aplica histerese; mantém multiplicador manual para restauração. | Ajusta quadros apresentados, não aumenta FPS renderizado nem garante menor atraso. Pode trocar qualidade/latência dinamicamente. Multiplicador fixo é melhor para o primeiro A/B. |
| Multipass | Cada passe executa o modelo, com histórico próprio; release amplia o limite a30. | Não priorizar em6GB: custo computacional e memória sobem. O autor cita cerca de200MB por passe em1440p, não medição no ASUS. |

Fontes: [presets e x8](https://github.com/Skynizz/optiscaler-dlss5/blob/fc702f8ca22f97b01bd6a02f3386bffb3484fc35/OptiScaler/dlssnr/DlssNr_Menu.cpp#L189), [Region](https://github.com/Skynizz/optiscaler-dlss5/blob/fc702f8ca22f97b01bd6a02f3386bffb3484fc35/OptiScaler/shaders/dlssnr/precompile/dlssnr.hlsl#L582), [cache](https://github.com/Skynizz/optiscaler-dlss5/blob/fc702f8ca22f97b01bd6a02f3386bffb3484fc35/OptiScaler/shaders/dlssnr/DlssNr_EditCache_Dx12.cpp), [algoritmo FG automático](https://github.com/Skynizz/optiscaler-dlss5/blob/fc702f8ca22f97b01bd6a02f3386bffb3484fc35/OptiScaler/framegen/synced/FGMeter.cpp#L342).

A [issue2](https://github.com/Skynizz/optiscaler-dlss5/issues/2) relata microstutter com Edit Cache+MFG e melhora ao desligar cache. É de12/10, anterior à1.3.0, continua aberta: não prova que a versão nova reproduza ou resolva o problema. A [issue6](https://github.com/Skynizz/optiscaler-dlss5/issues/6), de20/10, pergunta sobre NR nova sem resposta no registro consultado; pergunta não é prova de incompatibilidade.

## Diff com nossa pesquisa

A branch deste repositório `experiment/pre-sr-copy-optimization`, ponta `a1e9498d19e0fe51a149012ab82faf4bc67f7d29`, acrescenta à base apenas um documento de investigação e um workflow (três commits, nenhum C++/shader modificado). Não é uma otimização de renderização implementada ou validada. O [plano de cópias](https://github.com/edsonjdl/OptiScaler-DLSSNR-PreSR-Multipass/blob/a1e9498d19e0fe51a149012ab82faf4bc67f7d29/docs/experiments/pre-sr-copy-optimization.md) registra que ping-pong persistente já existe e que cópias de Color sem UAV ou com padding podem ser necessárias; redundância não foi demonstrada. Conferi agora os pontos concretos na base: `cropColor` condiciona CopyActiveColor de entrada (2264–2272) e retorno (2278–2285); `targetSupportsUav` escolhe escrita direta e só o ramo sem UAV faz CopyResource de retorno (2842–2909). Portanto, o atalho genérico de 'evitar cópia quando já há UAV' já existe nesse trecho; removê-lo sem medir as condições do Shadow seria especulativo. [Código fixado](https://github.com/edsonjdl/OptiScaler-DLSSNR-PreSR-Multipass/blob/1bd39091337cc07ba961c8e59ded57e21dc95b18/OptiScaler/shaders/dlssnr/DlssNr_Dx12.cpp#L2842). Portanto, a comparação de código Skynizz/base também cobre o código de renderização dessa branch, que foi preservada.

Separadamente, base do experimento local no projeto RTX4050: wilsjo2 `1bd39091337cc07ba961c8e59ded57e21dc95b18`; patches locais0001–0006 e cabeçalhos de diagnóstico, política de resolução e fences. A comparação das árvores base/Skynizz encontrou192 blobs modificados,91 adicionados e74 ausentes no destino. Isso mede inventário, não revisão integral de357 alterações nem compatibilidade automática de cherry-pick.

O experimento local usa resolução100/75/50 por eixo, parâmetros fixados por processo, um passe e proteção por fences para descritores/gerações; não implementa o cache temporal/Region do Skynizz. Os relatos anteriores de bypass e aplicação visual inconsistente impedem tratá-lo como baseline de qualidade validado. A instalação funcional do jogo é outro binário e deve continuar sendo a referência de uso.

Adaptações plausíveis para revisão futura, **não realizadas**:
- Medição/telemetria de FG e escolha explícita de flip metering: prioridade maior, desde que os hooks e a semântica sejam compatíveis.
- Cache NR: pacote próprio de avaliação, shader, constantes/configuração, movimentação/profundidade, invalidação e descarte de recursos; não copiar apenas um preset do menu.
- Region: opção isolada de composição/qualidade, acompanhada do shader compilado correspondente.
- Gestão de memória oficial: revisar por grupos de recursos e filas; não aplicar o commit amplo inteiro por inferência.

Os LICENSE da base e do Skynizz são byte-idênticos, blob `f288702d2fa16d3cdf0035b15a9fcbc552cd88e7`, GPL-3.0. Preservar avisos, fonte correspondente e licenças de terceiros numa futura distribuição. Os nove submódulos presentes no Skynizz conservam os commits correspondentes da base examinada; o gitmodule RTX40MFG-Unlock da base não aparece na lista do Skynizz. Isso não prova ausência de mecanismos próprios de MFG. NVIDIA NR é proprietária, fornecida separadamente; não incluir essas DLLs no Git ou em artefato aberto.

## Oficial: VRAM, estabilidade e medição

O commit relevante é [73deae913592b6197b4c08e6163835404c15d325](https://github.com/optiscaler/OptiScaler/commit/73deae913592b6197b4c08e6163835404c15d325), **17/10**, incluído na [nightly18/10](https://github.com/optiscaler/OptiScaler-nightly/releases/tag/nightly-20261018). Toca51 arquivos. O autor o apresenta como tentativa ampla com possibilidade de problemas, sem benchmark publicado nessa mensagem.

Mudanças concretas:
- libera cópias FG/HUD/auxiliares quando deixam de ser necessárias, inclusive ao desativar FG;
- evita algumas cópias redundantes e reduz recursos de callbacks FSR e Vulkan;
- deixa de criar Bias para DLSS/DLSSD e ajusta tamanho de cópia de profundidade no input DX12;
- introduz DeferredRelease e coleta no Present/avaliação.

**Limite de segurança:** DeferredRelease usa contagem de8 Presents com idade mínima500ms ou fallback2s; isso não é fence de conclusão GPU. A mudança substitui inclusive parte de um descarte HUD antes baseado em fences. Não portar esse mecanismo como garantia universal de conclusão, especialmente com múltiplas filas e NR. Não confundir redução de retenção de buffers com redução do modelo NR, do jogo inteiro ou do custo de RT. Buffers que o FG ativo ainda exige permanecem necessários.

A [nightly27/10](https://github.com/optiscaler/OptiScaler-nightly/releases/tag/nightly-20261027) inclui [7937e473](https://github.com/optiscaler/OptiScaler/commit/7937e473776bf49b6b54761e608912a9c909bdc3), que corrige contabilização de Presents/frametimes. **Corrigir o contador não gera quadros mais rápido.** Outras mudanças dessa release envolvem Vulkan/FFX, XeFG e carregamento de features de jogos específicos. Não justificam migrar este Shadow DX12/DLSSG por si sós.

A [nightly30/10](https://github.com/optiscaler/OptiScaler-nightly/releases/tag/nightly-20261030), HEAD examinado `e7e3872d410f704479d94d318d68d163b6b58947`, acrescenta carregamento customizado de DLSSG e correções de outros jogos/Vulkan. Na árvore oficial examinada não há módulos dlssnr/neural-rendering equivalentes aos do fork; substituir o fork pela build oficial **não é troca transparente para preservar NR**. Não confundir SR/RR/FG com NR.

## Até dois testes futuros, sujeitos a aprovação

1. **Pacing com NR preservado.** Primeiro capturar referência do perfil atual; depois comparar apenas NVIDIA flip metering em um caminho que comprovadamente aceite o controle. Manter mesma DLL NR, SR E/Performance, resolução4K, RT Ultra, cena e multiplicador fixo. Usar logs para confirmar o valor aplicado. Verificar se o perfil já desativa a função para não repetir teste equivalente. Se exigir adaptação/binário, apresentar diff/build antes de pedir instalação. Não ligar auto multiplier nessa comparação.
2. **Cache NR conservador, se ainda houver necessidade de FPS renderizado.** Comparar NR todos os quadros contra intervalo2, Pre-SR, escala1.0, um passe, composição/intensidade iguais, FG desligado primeiro. É um perfil manual proposto, não o preset x8/Performance pronto do menu. Exige candidato compatível, recursos e rollback revisados. Se aparecer efeito enfraquecido, rastro, pulsação ou VRAM maior com stutter, reprovar.

x8, Region, multipass e auto multiplier ficam fora desses dois primeiros testes. Não há proposta de nova DLL NR: a cópia fornecida já é a instalada.

Protocolo: jogo fechado para backups/transações; guardar INI integral e hashes dos arquivos a alterar, incluindo arquivos inicialmente ausentes. Aquecimento e3 capturas de60s por condição na mesma rota/câmera, sem telefonema/alt-tab/mudança de modo no meio. Registrar média,1% low e p95/p99 dos tempos renderizados; NR GPU ms; pico de VRAM dedicada e orçamento; FPS/Presents entregues e intervalos com FG. Sem instrumento existente adequado, registrar essa lacuna, não instalar ferramentas automaticamente. Capturas visuais estáticas e em movimento em rosto, cabelos, folhagem, sombra e desoclusões. Uma porcentagem de ganho só é válida contra as mesmas condições, separando FPS gerado de renderizado. Avaliação subjetiva de input lag não equivale a medição física. Em falha, preservar logs, encerrar pelo usuário e restaurar hashes; não apagar backups.

## Estado e próxima decisão

Análise/documentação concluídas no escopo acima; desempenho/qualidade reais continuam não medidos nesta rodada. A decisão a pedir ao usuário é se deseja preparar o primeiro teste de pacing ou continuar usando o perfil funcional sem novos testes. Nenhuma compilação foi disparada/acompanhada, nenhuma alteração de código integrada e nenhuma autorização antiga foi reaproveitada para instalar ou testar.
