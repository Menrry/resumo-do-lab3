# Configurando Recursos e Dimensionamento de Máquinas Virtuais no Azure

Este repositório contém o resumo das lições aprendidas durante o desenvolvimento do lab na DIO. Demonstra como configurar recursos e dimensionar Máquinas Virtuais (VMs) no Microsoft Azure, desde a criação básica até a otimização de custos e desempenho.

## 1. Criando uma Máquina Virtual (VM)

O primeiro passo é provisionar uma nova VM.

### 1.1. Acessar o Portal do Azure

1.  Abra seu navegador e navegue até o [Portal do Azure](https://portal.azure.com).
2.  Faça login com sua conta da Microsoft.

### 1.2. Iniciar a Criação da VM

1.  No painel de navegação esquerdo, clique em "Máquinas virtuais".
2.  Clique em "+ Criar" e selecione "Máquina virtual".

### 1.3. Configurar as Informações Básicas

Preencha os detalhes na guia "Noções básicas":

* **Assinatura:** Selecione a assinatura Azure que você deseja usar.
* **Grupo de recursos:** Crie um novo grupo de recursos ou selecione um existente. Um grupo de recursos é um contêiner lógico para seus recursos do Azure.
* **Nome da máquina virtual:** Dê um nome exclusivo para sua VM (ex: `minha-vm-producao`).
* **Região:** Escolha a região geográfica onde sua VM será hospedada (ex: `Brazil South`). Selecionar uma região próxima aos seus usuários pode reduzir a latência.
* **Opções de disponibilidade:** Para ambientes de produção, considere "Conjunto de disponibilidade" ou "Zonas de disponibilidade" para garantir alta disponibilidade.
* **Tipo de segurança:** Mantenha "Standard" para a maioria dos casos.
* **Imagem:** Selecione o sistema operacional desejado (ex: `Windows Server 2022 Datacenter - Gen2`, `Ubuntu Server 22.04 LTS`).
* **Tamanho:** Esta é a parte crucial para o dimensionamento inicial. Veja a seção 2 para mais detalhes. Para começar, você pode selecionar um tamanho padrão como `Standard_B2s` (2 vCPUs, 4 GiB de memória).
* **Nome de usuário:** Crie um nome de usuário para acessar a VM.
* **Senha:** Crie uma senha forte para o nome de usuário.
* **Portas de entrada públicas:** Para fins de teste, você pode selecionar "Permitir portas selecionadas" e escolher `HTTP (80)` e `RDP (3389)` (para Windows) ou `SSH (22)` (para Linux). Em um ambiente de produção, é recomendável configurar um Network Security Group (NSG) para controlar o tráfego de forma mais granular.

### 1.4. Configurar Discos

Na guia "Discos":

* **Tipo de disco de SO:** Escolha entre `SSD Premium`, `SSD Standard` ou `HDD Standard`. Para desempenho, `SSD Premium` é o mais recomendado.
* **Discos de dados:** Adicione discos de dados se precisar de armazenamento adicional. Você pode anexar vários discos com diferentes tipos e tamanhos.

### 1.5. Configurar Rede

Na guia "Rede":

* **Rede virtual:** Crie uma nova rede virtual ou selecione uma existente.
* **Sub-rede:** Crie uma nova sub-rede ou selecione uma existente dentro da sua rede virtual.
* **IP público:** Crie um novo IP público ou selecione um existente. Este IP será usado para acessar a VM da internet.
* **Grupo de segurança de rede (NSG) da placa de interface de rede (NIC):** "Básico" para criar um NSG automaticamente com as portas que você selecionou. Para maior controle, escolha "Avançado" e configure as regras do NSG manualmente.

### 1.6. Revisar e Criar

1.  Passe pelas outras guias (Gerenciamento, Monitoramento, Avançado, Tags) para configurar opções adicionais se necessário.
2.  Na guia "Revisar + criar", revise todas as suas configurações.
3.  Clique em "Criar" para provisionar sua VM.

## 2. Dimensionamento de Máquinas Virtuais (VMs)

O dimensionamento é o processo de ajustar os recursos da sua VM para atender às demandas de carga de trabalho. O Azure oferece dimensionamento vertical (escalabilidade) e horizontal (conjuntos de escala).

### 2.1. Entendendo os Tipos de Tamanho da VM

O Azure oferece uma vasta gama de tamanhos de VM, cada um otimizado para diferentes cargas de trabalho. Eles são categorizados por famílias:

* **Série B (Burstable):** Ideal para cargas de trabalho que não precisam de desempenho total da CPU continuamente, mas podem ter picos. Econômicas para ambientes de desenvolvimento/teste e pequenos servidores web.
* **Série D (Propósito Geral):** Um bom equilíbrio entre CPU e memória. Adequadas para a maioria das cargas de trabalho de produção, como aplicativos web, servidores de banco de dados pequenos e médios.
* **Série E (Otimizadas para Memória):** Projetadas para cargas de trabalho que exigem grandes quantidades de memória, como bancos de dados em memória e grandes caches.
* **Série F (Otimizadas para Computação):** Foco em alta proporção CPU-memória, ideais para servidores web, processamento de lote e codificação de vídeo.
* **Série M (Otimizadas para Memória Extrema):** Para cargas de trabalho de banco de dados extremas, como SAP HANA.
* **Série N (GPU Habilitadas):** Projetadas para cargas de trabalho intensivas em gráficos e computação de aprendizado de máquina.

Para ver a lista completa e detalhes de cada série, consulte a [documentação de tamanhos de VM do Azure](https://docs.microsoft.com/pt-br/azure/virtual-machines/sizes).

### 2.2. Dimensionamento Vertical (Escalabilidade)

O dimensionamento vertical (ou *scale up/down*) envolve o aumento ou diminuição dos recursos de uma única VM (CPU, memória).

#### 2.2.1. Alterando o Tamanho de uma VM Existente

1.  No Portal do Azure, navegue até a sua máquina virtual.
2.  No menu esquerdo da VM, em "Configurações", clique em "Tamanho".
3.  Você verá uma lista de tamanhos de VM disponíveis para sua região e tipo de VM.
4.  Selecione o novo tamanho desejado. O Azure mostrará os recursos (vCPUs, memória) e o custo estimado.
5.  Clique em "Redimensionar".

* **Observação:** A VM será reiniciada durante o processo de redimensionamento. Isso causará um tempo de inatividade. Planeje essa operação durante um período de baixa demanda.

### 2.3. Dimensionamento Horizontal (Conjuntos de Escala de Máquinas Virtuais)

O dimensionamento horizontal (ou *scale out/in*) envolve a adição ou remoção de instâncias de VM. Isso é ideal para cargas de trabalho que podem ser distribuídas entre várias VMs, como aplicativos web sem estado. O Azure oferece Conjuntos de Escala de Máquinas Virtuais para gerenciar isso.

#### 2.3.1. Criando um Conjunto de Escala de Máquinas Virtuais

1.  No Portal do Azure, clique em "+ Criar um recurso".
2.  Procure por "Conjunto de escala de máquinas virtuais" e clique em "Criar".
3.  Siga o assistente para configurar seu conjunto de escala:
    * **Noções básicas:** Nome do conjunto de escala, região, sistema operacional.
    * **Dimensionamento:**
        * **Tipo de dimensionamento:** "Manual" (você ajusta o número de instâncias) ou "Personalizado" (baseado em métricas de desempenho).
        * **Contagem de instâncias inicial:** Defina o número mínimo de VMs.
        * **Mínimo de instâncias:** O número mínimo de VMs que o conjunto de escala manterá.
        * **Máximo de instâncias:** O número máximo de VMs que o conjunto de escala pode escalar.
        * **Regras de dimensionamento:** Crie regras baseadas em métricas como uso da CPU, uso da memória, comprimento da fila do armazenamento, etc., para adicionar ou remover automaticamente instâncias. Por exemplo, "Se a CPU média for maior que 75% por 5 minutos, adicione 1 instância".
    * **Rede:** Configure a rede virtual e o balanceador de carga para distribuir o tráfego entre as instâncias.

#### 2.3.2. Gerenciando o Dimensionamento Automático

Depois de criar um conjunto de escala, você pode ajustar as regras de dimensionamento automático:

1.  No Portal do Azure, navegue até seu conjunto de escala de máquinas virtuais.
2.  No menu esquerdo, em "Configurações", clique em "Dimensionamento".
3.  Aqui você pode modificar as regras existentes, adicionar novas regras ou alterar o número de instâncias manualmente (se o tipo de dimensionamento for "Manual").

## 3. Otimização de Custos e Desempenho

### 3.1. Monitoramento

Use o Azure Monitor para acompanhar o desempenho da sua VM e identificar gargalos:

1.  No Portal do Azure, navegue até sua VM.
2.  No menu esquerdo, em "Monitoramento", clique em "Métricas".
3.  Adicione gráficos para métricas como `Porcentagem da CPU`, `Bytes de Rede`, `Operações de E/S de Disco por Segundo` (IOPS) e `Porcentagem de Memória`.
4.  Defina alertas para ser notificado quando as métricas excederem os limites.

### 3.2. Desligamento Automático

Para VMs de desenvolvimento/teste, configure o desligamento automático para economizar custos:

1.  No Portal do Azure, navegue até sua VM.
2.  No menu esquerdo, em "Operações", clique em "Desligamento automático".
3.  Habilite o desligamento automático e defina uma hora diária.

### 3.3. Reservas do Azure

Se você tem cargas de trabalho previsíveis que serão executadas por um longo período (1 ou 3 anos), considere as Reservas do Azure para obter grandes descontos em comparação com as taxas de pagamento conforme o uso.

### 3.4. Azure Advisor

O Azure Advisor fornece recomendações personalizadas para otimizar custos, desempenho, alta disponibilidade, segurança e excelência operacional.

1.  No Portal do Azure, procure por "Advisor".
2.  Analise as recomendações para suas VMs.

## Conclusão

Configurar e dimensionar máquinas virtuais no Azure é um processo flexível que permite adaptar seus recursos às necessidades da sua aplicação. Ao entender os diferentes tamanhos de VM, utilizar o dimensionamento vertical e horizontal, e monitorar o desempenho, você pode garantir que suas VMs funcionem de forma eficiente e econômica.

---
