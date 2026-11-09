# Feature Specification: Editar e Excluir Atualizações da Obra

**Feature Branch**: `[006-edit-delete-work-updates]`

**Created**: 2026-11-09

**Status**: Draft

**Input**: User description: "Permitir que a responsável pelo escritório autenticada no aplicativo móvel gerencie a linha do tempo da obra por meio da edição de dados e mídias ou da exclusão permanente de atualizações de progresso previamente publicadas pelo seu próprio escritório, garantindo flexibilidade para correções operacionais dentro de uma janela limite de 24 horas a partir da criação e atualizando a linha do tempo da obra imediatamente."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Editar atualização publicada há menos de 24 horas (Priority: P1)

A responsável pelo escritório autenticada abre a linha do tempo da obra, identifica uma atualização publicada pelo seu escritório há menos de 24 horas e escolhe a opção de editar. O formulário abre pré-preenchido com o ambiente, a legenda e a coleção de mídias atuais. Ela ajusta o ambiente (obrigatório), altera a legenda (opcional) e modifica as mídias adicionando novos arquivos da galeria ou câmera, removendo arquivos existentes ou substituindo parte deles. Ao confirmar, o aplicativo revalida as regras da atualização, envia a alteração, exibe mensagem de sucesso, fecha o formulário e atualiza o cartão correspondente na linha do tempo imediatamente.

**Why this priority**: É o núcleo da correção operacional — permite corrigir informações e mídias publicadas incorretamente sem perder o histórico da obra.

**Independent Test**: Pode ser testada abrindo uma atualização elegível, alterando ambiente, legenda e mídias, confirmando a edição e verificando que o cartão é atualizado imediatamente na linha do tempo e que os dados persistidos correspondem ao que foi enviado.

**Acceptance Scenarios**:

1. **Given** a responsável autenticada visualiza uma atualização do seu escritório criada há menos de 24 horas, **When** ela abre o menu de opções do cartão e escolhe editar, **Then** o formulário abre pré-preenchido com o ambiente, a legenda e as mídias atuais da atualização.
2. **Given** o formulário de edição está aberto, **When** a responsável altera o ambiente para um valor não vazio, ajusta a legenda e/ou modifica a coleção de mídias dentro das regras, **Then** a confirmação é permitida e a alteração é enviada.
3. **Given** a responsável remove todas as mídias existentes e não adiciona nenhuma nova, **When** ela tenta confirmar, **Then** o aplicativo bloqueia o envio e exibe alerta visual de que é necessário ao menos 1 mídia.
4. **Given** a responsável adiciona mídias de modo que a coleção final ultrapasse 5 arquivos, possua mais de 1 vídeo ou contenha vídeo com duração superior a 30 segundos, **When** ela tenta confirmar, **Then** o aplicativo bloqueia o envio e destaca o alerta correspondente.
5. **Given** a responsável mantém mídias já publicadas e adiciona novas mídias locais respeitando as regras, **When** ela confirma, **Then** a coleção final submetida contém as mídias mantidas e as novas mídias, na ordem exibida no formulário.
6. **Given** a alteração é aceita pelo servidor, **When** a resposta de sucesso é recebida, **Then** o aplicativo exibe mensagem de sucesso, fecha o formulário e atualiza imediatamente o cartão correspondente (ambiente, legenda e mídias) na linha do tempo.
7. **Given** a alteração é aceita, **When** o cartão é atualizado, **Then** a data de criação original da atualização é preservada e a janela de 24 horas continua contada a partir da criação original.

---

### User Story 2 - Excluir permanentemente uma atualização publicada há menos de 24 horas (Priority: P1)

A responsável pelo escritório autenticada abre o menu de opções de uma atualização elegível, escolhe excluir e recebe uma confirmação explícita informando que a remoção é irrecuperável. Ao confirmar, o aplicativo solicita a exclusão, exibe mensagem de sucesso e remove o cartão da linha do tempo imediatamente. Se ela cancelar, nada é alterado.

**Why this priority**: Permite descartar registros publicados por engano dentro da janela de correção, mantendo a linha do tempo confiável.

**Independent Test**: Pode ser testada escolhendo excluir uma atualização elegível, confirmando a remoção e verificando que o cartão desaparece imediatamente do feed e que a atualização não retorna após recarregar a linha do tempo.

**Acceptance Scenarios**:

1. **Given** a responsável autenticada abre o menu de opções de uma atualização elegível, **When** ela escolhe excluir, **Then** o aplicativo solicita confirmação explícita informando que a remoção é permanente e irrecuperável.
2. **Given** a confirmação de exclusão está visível, **When** a responsável cancela, **Then** nenhuma exclusão é solicitada e o cartão permanece inalterado na linha do tempo.
3. **Given** a confirmação de exclusão está visível, **When** a responsável confirma, **Then** o aplicativo solicita a exclusão, exibe mensagem de sucesso e remove o cartão da linha do tempo imediatamente.
4. **Given** a exclusão foi concluída, **When** a linha do tempo é recarregada, **Then** a atualização excluída não volta a aparecer.

---

### User Story 3 - Exibição condicional das opções por autoria e janela de 24 horas (Priority: P2)

O aplicativo exibe as opções de editar e excluir exclusivamente em cartões de atualizações criadas pelo escritório da responsável autenticada e com menos de 24 horas desde a criação. Atualizações de outros escritórios ou criadas há 24 horas ou mais não oferecem essas opções.

**Why this priority**: Garante isolamento entre escritórios e respeito à janela de correção, evitando ações indevidas ou fora do prazo.

**Independent Test**: Pode ser testada renderizando cartões com diferentes autores e idades e verificando que as opções aparecem somente nos cartões do escritório autenticado com menos de 24 horas.

**Acceptance Scenarios**:

1. **Given** um cartão de atualização criado pelo escritório da responsável há menos de 24 horas, **When** o cartão é renderizado, **Then** as opções de editar e excluir são exibidas.
2. **Given** um cartão de atualização criado por outro escritório, **When** o cartão é renderizado, **Then** as opções de editar e excluir não são exibidas.
3. **Given** um cartão de atualização do escritório da responsável criado há exatamente 24 horas ou mais, **When** o cartão é renderizado ou a linha do tempo é atualizada, **Then** as opções de editar e excluir não são exibidas.
4. **Given** o servidor recusa uma edição ou exclusão por expiração da janela de 24 horas, **When** a recusa é recebida, **Then** o aplicativo informa que o registro foi consolidado e remove as opções de alteração do cartão, mantendo o registro na linha do tempo.

---

### User Story 4 - Tratamento de falhas durante edição e exclusão (Priority: P2)

O aplicativo trata falhas de rede, de processamento de mídia local e de registros indisponíveis sem perder o trabalho da responsável e sem exibir avisos enganosos.

**Why this priority**: Preserva a confiança na operação e evita retrabalho em condições adversas.

**Independent Test**: Pode ser testada simulando perda de conexão, arquivo de mídia corrompido e registro removido por outra sessão, verificando que o formulário e os dados são preservados e que as mensagens são corretas.

**Acceptance Scenarios**:

1. **Given** a responsável preencheu o formulário de edição e selecionou mídias, **When** o envio falha por falta de conexão, **Then** o aplicativo avisa sobre a perda de conexão e preserva todos os dados digitados e arquivos selecionados para nova tentativa.
2. **Given** a responsável seleciona um arquivo de mídia corrompido ou incompatível no dispositivo, **When** o processamento local falha, **Then** o aplicativo exibe alerta indicando o problema sem fechar o formulário e preservando os demais dados já preenchidos.
3. **Given** uma atualização foi removida por outra sessão enquanto a responsável a editava, **When** o servidor informa que o registro não existe mais, **Then** o aplicativo informa a indisponibilidade, fecha o formulário e remove o cartão da linha do tempo.
4. **Given** uma edição falha por erro interno do servidor, **When** a resposta de erro é recebida, **Then** o aplicativo exibe uma mensagem de falha genérica sem afirmar falsamente que houve perda de conexão, preservando os dados do formulário.

---

### Edge Cases

- **Janela no limite exato**: uma atualização criada há exatamente 24 horas não é elegível para edição ou exclusão.
- **Relógio do dispositivo divergente**: a elegibilidade final é determinada pelo servidor; quando o servidor recusa por expiração, o aplicativo consolida o registro e remove as opções do cartão.
- **Atualização de outro escritório**: mesmo que o cartão seja exibido, as opções de editar e excluir não aparecem e qualquer tentativa direta é recusada pelo servidor.
- **Coleção de mídias vazia após remoções**: o envio é bloqueado com alerta visual de mínimo de 1 mídia.
- **Coleção com mais de 5 mídias**: o envio é bloqueado com alerta visual de limite máximo.
- **Coleção com mais de 1 vídeo**: o envio é bloqueado com alerta visual indicando que apenas 1 vídeo é permitido.
- **Vídeo acima de 30 segundos**: o envio é bloqueado com alerta visual de duração máxima.
- **Foto fora de JPEG/PNG, vídeo fora de MP4/MOV, foto acima de 5 MB ou vídeo acima de 20 MB**: o envio é bloqueado com o alerta correspondente.
- **Arquivo local corrompido ou ilegível**: alerta exibido, formulário permanece aberto e os dados já preenchidos são preservados.
- **Falha de conexão durante envio**: dados e mídias são preservados para nova tentativa; nenhuma atualização parcial é exibida.
- **Exclusão concorrente**: se o registro já não existir, o cartão é removido da linha do tempo e a responsável é informada.
- **Ambiente contendo apenas espaços em branco**: tratado como não preenchido e o envio é bloqueado.
- **Legenda apagada durante a edição**: a atualização passa a ser exibida sem bloco de legenda, sem bloquear o envio.
- **Vídeo publicado mantido na edição**: a duração conhecida do vídeo existente é considerada na revalidação das regras.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: O sistema MUST exibir um menu de opções nos cartões de atualização da linha do tempo, contendo as ações de editar e excluir quando o cartão for elegível.
- **FR-002**: O sistema MUST considerar elegível apenas a atualização criada pelo escritório da responsável autenticada e com menos de 24 horas desde a criação.
- **FR-003**: O sistema MUST ocultar as opções de editar e excluir em atualizações de outros escritórios e em atualizações criadas há 24 horas ou mais.
- **FR-004**: O sistema MUST abrir o formulário de edição pré-preenchido com o ambiente, a legenda e a coleção de mídias atuais da atualização selecionada.
- **FR-005**: O sistema MUST exigir ambiente não vazio (considerando espaços em branco como vazio) na edição e MUST permitir legenda vazia.
- **FR-006**: O sistema MUST permitir que a coleção final de mídias contenha de 1 a 5 arquivos, no máximo 1 vídeo, vídeos com duração máxima de 30 segundos, fotos nos formatos JPEG ou PNG, vídeos nos formatos MP4 ou MOV, fotos de até 5 MB e vídeos de até 20 MB.
- **FR-007**: O sistema MUST permitir, no formulário de edição, adicionar novas fotos ou vídeos da galeria ou câmera, remover individualmente mídias existentes e manter mídias já publicadas sem exigir novo envio desses arquivos.
- **FR-008**: O sistema MUST bloquear a submissão da edição e destacar alertas visuais quando a composição final de mídias violar qualquer regra da FR-006.
- **FR-009**: O sistema MUST enviar a edição com o ambiente, a legenda e a coleção final de mídias (mídias mantidas e novas) na ordem exibida no formulário.
- **FR-010**: O sistema MUST, após confirmação do servidor, exibir mensagem de sucesso, fechar o formulário e atualizar imediatamente o cartão correspondente na linha do tempo com os novos dados e mídias.
- **FR-011**: O sistema MUST preservar a data de criação original da atualização após uma edição, mantendo a janela de 24 horas contada a partir da criação original.
- **FR-012**: O sistema MUST exigir confirmação explícita, informando que a remoção é permanente e irrecuperável, antes de solicitar a exclusão de uma atualização.
- **FR-013**: O sistema MUST cancelar a exclusão sem efeitos colaterais quando a responsável recusar a confirmação.
- **FR-014**: O sistema MUST, após confirmação do servidor, exibir mensagem de sucesso e remover imediatamente o cartão da linha do tempo.
- **FR-015**: O sistema MUST, ao receber recusa do servidor por expiração da janela de 24 horas, informar que o registro foi consolidado e remover as opções de alteração do cartão.
- **FR-016**: O sistema MUST preservar todos os dados digitados e arquivos selecionados no formulário quando uma edição falhar por falta de conexão, permitindo nova tentativa manual.
- **FR-017**: O sistema MUST exibir alerta e manter o formulário aberto, preservando os demais dados, quando a seleção ou o processamento de um arquivo local falhar por corrupção ou incompatibilidade.
- **FR-018**: O sistema MUST tratar a indisponibilidade de um registro durante edição ou exclusão informando a responsável e removendo o cartão da linha do tempo.
- **FR-019**: O sistema MUST distinguir falhas de conexão de falhas internas do servidor, exibindo mensagem adequada a cada caso sem avisos enganosos.
- **FR-020**: O sistema MUST registrar eventos-chave de edição e exclusão (sucesso e falha) para observabilidade, sem registrar dados sensíveis.
- **FR-021**: O sistema MUST garantir que atualizações fora da janela de 24 horas permaneçam imutáveis no aplicativo, sem opções de edição, exclusão ou restauração.
- **FR-022**: O sistema MUST manter as opções de editar e excluir ocultas até que a linha do tempo ou o cartão sejam atualizados, quando a elegibilidade for reavaliada.

### Key Entities *(include if feature involves data)*

- **Atualização (Update)**: Registro de progresso da obra, pertencente a uma obra e ao escritório da responsável autenticada. Possui identificador, ambiente, legenda, coleção de mídias, data de criação e autoria. A elegibilidade para edição/exclusão depende da autoria e de a data de criação estar a menos de 24 horas do momento da ação.
- **Ambiente (Environment)**: Nome do cômodo ou área da obra. Obrigatório na edição; valores vazios ou apenas com espaços são inválidos.
- **Legenda da Atualização (Caption)**: Descrição textual opcional do progresso ou ocorrência da obra; pode ser alterada ou removida.
- **Mídia Publicada (Published Media)**: Foto ou vídeo já associado à atualização, identificável individualmente, que pode ser mantido ou removido durante a edição.
- **Mídia Nova (New Media)**: Foto ou vídeo selecionado no dispositivo durante a edição, ainda não associado à atualização, que passa a compor a coleção final.
- **Coleção de Mídias (Media Collection)**: Conjunto final ordenado de mídias publicadas mantidas e mídias novas, sujeito às invariantes de quantidade, tipo, formato, duração e tamanho.
- **Responsável (Responsible)**: Usuária autenticada no aplicativo. A autenticação é por escritório; a autoria de uma atualização corresponde ao escritório da sessão. Qualquer sessão autenticada desse escritório pode gerenciar as atualizações elegíveis do próprio escritório.
- **Janela de Correção (Correction Window)**: Intervalo de 24 horas contado a partir da criação original da atualização, durante o qual edição e exclusão são permitidas. A expiração é determinada pelo servidor.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: 100% das atualizações de outros escritórios e das atualizações com 24 horas ou mais não exibem opções de edição ou exclusão.
- **SC-002**: A responsável consegue concluir uma edição válida em menos de 2 minutos, incluindo a alteração de ambiente, legenda e mídias.
- **SC-003**: O cartão editado reflete os novos dados e mídias em até 1 segundo após a confirmação do servidor, sem exigir recarga manual da linha do tempo.
- **SC-004**: O cartão excluído desaparece da linha do tempo em até 1 segundo após a confirmação do servidor e não reaparece após recarregar a lista.
- **SC-005**: 100% das tentativas de submissão com composição de mídia inválida são bloqueadas e exibem o alerta correspondente.
- **SC-006**: 100% das falhas de conexão durante o envio preservam os dados digitados e as mídias selecionadas no formulário.
- **SC-007**: 100% das recusas por expiração da janela de 24 horas resultam em aviso de registro consolidado e remoção das opções de alteração do cartão.
- **SC-008**: Nenhuma edição ou exclusão de atualização de outro escritório é concluída com sucesso.
- **SC-009**: 95% das operações de edição e exclusão válidas e elegíveis são concluídas com sucesso na primeira tentativa.

## Assumptions

- A responsável já está autenticada no aplicativo antes de acessar a linha do tempo e o modelo de autenticação permanece por escritório, de modo que a autoria da atualização corresponde ao escritório da sessão.
- O servidor é a autoridade final da janela de 24 horas; a verificação no aplicativo é apenas de exibição e pode divergir se o relógio do dispositivo estiver incorreto.
- O backend passará a oferecer operações de edição e exclusão de atualizações com validação de autoria e de janela temporal; o aplicativo não é a autoridade final dessas regras.
- A janela de correção é ancorada na data de criação original e não é reiniciada por edições.
- As mídias já publicadas podem ser mantidas sem novo upload e identificadas individualmente; a coleção final é submetida com as mídias mantidas e as novas mídias na ordem exibida no formulário.
- As regras e mensagens canônicas de mídia permanecem as da feature 005 (`specs/005-work-progress-feed/data-model.md` §3.2 e §6): 1 a 5 arquivos, no máximo 1 vídeo, vídeo de até 30 segundos, JPEG/PNG para fotos, MP4/MOV para vídeos, foto de até 5 MB e vídeo de até 20 MB.
- As URLs de mídia continuam públicas, acessíveis por qualquer pessoa com o link, sem controle de acesso adicional além da obscuridade do URL.
- O aplicativo reutiliza os padrões existentes de feedback de sucesso, alerta e preservação de formulário.
- A exclusão é definitiva; não há lixeira, restauração ou desfazer após a confirmação.
- Não há salvamento de rascunhos de edição; fechar o formulário descarta as alterações não confirmadas.
- Não há edição avançada de vídeo, corte de duração ou filtros de foto nesta fase.
- Não há requisitos de acessibilidade explícitos adicionais nesta fase (nível mínimo), mantendo o padrão atual do aplicativo.
- A conexão de rede é normalmente estável, mas pode apresentar interrupções durante a transmissão.
- Esta funcionalidade substitui a regra de imutabilidade total da feature 005 (FR-015): as atualizações passam a ser editáveis e excluíveis exclusivamente dentro da janela de 24 horas e pelo escritório autor; após a janela, permanecem imutáveis.
