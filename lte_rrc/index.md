# LTE 系列：RRC 层


> LTE RRC 层详解

<!--more-->

## 无线资源控制（RRC）

无线资源控制（Radio Resource Control，RRC）层是 LTE 无线接入网协议中**控制面**部分的主要内容。

用户终端在进行数据通信之前，需要与网络建立承载信令消息的连接，通过信令消息的交互，控制数据通信的过程。因此，**是否与网络建立了 RRC 连接**是终端状态的判断依据。

- **RRC 连接状态**即已经与网络建立了 RRC 连接的终端处于连接状态，此时终端可以进行**数据传输**、**系统信息接收**，以及**邻小区的测量上报和小区切换**
- **RRC 空闲状态**即没有与网络建立 RRC 连接，那么终端处于空闲状态，此时终端**不进行数据的传输**，仍然需要执行的功能包括：**系统信息的接收**、**侦听寻呼**，以及**邻小区测量和小区重选**

RRC 协议包括以下的主要功能：

- 系统信息的广播，包括接入网的系统广播消息，以及核心网非接入层（NAS）系统信息的广播
- 连接控制，包括信令承载（即 RRC 连接）的建立／重配置／重建立／释放、数据无线承载的建立／配置／释放，以及相关的完整性保护的数据加密的安全机制
- 移动性管理，包括配置终端的测量和上报，以及终端的小区选择、重选、寻呼和切换过程
- 服务质量（QoS）管理
- NAS 信令直接传输的功能

### 系统信息的广播

系统信息描述网络的基本情况，采用广播的方式进行发送，对应于广播控制逻辑信道（BCCH）。终端通过对系统信息的接收，获得在系统中进行通信所需要的基本网络参数。

LTE 的系统信息分为：

- `主信息块`（Master Information Block，MIB）

  **主信息块**（MIB）包括小区最基本的物理层信息，例如：系统下行信号的带宽、无线帧序号和发送天线的数目。终端需要获得这些基本的信息才能够进一步接收系统的其他信息。

  `MIB` 信息在专门定义的广播传输信道（BCH）上，采用物理层广播信道（PBCH）在固定的时间和频率位置进行传输。`MIB` 采用 40ms 的周期重复发送，每个周期之内包括 4 次间隔为 10ms 的传输，在序号 SFN%4=0 无线帧的第 0 个子帧开始每个周期的第一次传输，其余 3 次传输发生在随后 3 个无线帧的第 0 个子帧的位置。

- `系统信息块`（System Information Block，SIB）

  **系统信息块**（SIB）映射在下行共享传输信道（DL-SCH）上进行传输，采用 `SI-RNTI` 进行指示。根据所传输信息的不同，`SIB` 分为多种类型：`Type 1` ～ `Type 12`

  - `SIB1`（Type 1 信息）：包括了是否允许终端接入小区的相关信息，例如系统的公众网络标识（PLMN）、小区标识、关闭的用户组（CSG）信息以及 TDD 系统上下行时隙比例的配置信息。SIB1 中还承载了其他系统信息块（SIB2 ～ SIB13）的调度信息
  - `SIB2`：承载用于所有终端公共的无线资源配置信息，例如系统上行信号的频率／带宽、MBSFN 子帧、随机接入信道、寻呼信道、上行 sounding 导频和上行功率控制等的配置情况
  - `SIB3` ～ `SIB8`：承载用于终端进行小区重选的相关参数，分别针对于同频、异频和异系统 3G/GSM/cdma2000 各种不同的情况。SIB9 指示关于家庭基站名字的信息
  - `SIB10` ～ `SIB12`：用于公共灾害的警示服务，即地震和海啸警报系统（Earthquake and Tsunami Warning System，ETWS）和商用移动警报服务（Commercial MobileAlert Service，CMAS）

`SIB1` 信息使用固定的时间位置进行传输，采用 80ms 的周期重复发送，每个周期之内包括 4 次间隔为 20ms 的传输，在序号 SFN%8=0 无线帧的第 5 个子帧开始每个周期的第一次传输，其余 3 次传输发生在随后 3 个序号为偶数的无线帧的第 5 个子帧的位置。

除了 `SIB1` 之外，其余的 `SIB` 消息映射在系统信息（System Information，SI）上进行传输，它们的映射关系可以进行灵活的配置，由 `SIB1` 中承载的调度信息进行指示。一个 `SIB` 消息只能映射在一个 `SI` 上进行传输，而一个 `SI` 可以包含多个具有相同周期的 `SIB` 消息。

系统广播信息向传输信道的映射如图所示:

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_22.webp" caption="系统广播信息向传输信道的映射">}}

系统广播信息的更新采用`更新提示`和`信息更新`两个阶段的方式。

根据系统配置的更新周期（最小值为 640ms），当需要对系统广播消息进行更新的时候，系统在前一个更新周期使用寻呼消息提示用户，系统广播消息将发生改变，但是在这个更新周期中，仍然发送原来的系统信息。在下一个更新周期，将发送改变之后的系统信息，终端根据该信息进行更新。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_23.webp" caption="系统广播信息的更新">}}

### 对连接的控制

**连接控制**指的是对接入网中的无线承载进行控制的功能，包括**信令承载的建立**／**重配置**／**重建立**／**释放**、**数据承载的建立**／**配置**／**释放**，以及相关的**完整性保护**和**数据加密**的通信安全机制。

#### 连接管理

LTE 中一共定义了 3 个用于信令的无线承载（Signaling Radio Bearer，SRB）：

- `SRB0` 用于承载公用控制逻辑信道（CCCH）的 RRC 消息
- `SRB1` 主要用于承载专用控制逻辑信道（DCCH）的 RRC 消息

  通常所说的建立 RRC 连接指的是信令无线承载 SRB1 的建立。

- `SRB2` 用于承载专用控制逻辑信道（DCCH）的核心网非接入层（NAS）控制信息。

终端开机在网络中进行注册之后处于空闲状态，终端首先建立传输 `RRC` 消息的信令承载 `SRB1`，终端进入 `RRC 连接状态`，然后，系统建立传输 `NAS` 消息的信令无线承载 `SRB2` 和传输数据的无线承载 `DRB`，由此可以开始进行数据的通信。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_24.webp" caption="RRC 连接建立的过程">}}

进入 `RRC 连接状态`之后，采用 `RRC 连接重配置`的过程来进行 `SRB2` 和 `DRB` 的建立和管理。

重配置过程在连接状态下对 RRC 连接进行更改，包括：

- 建立／更改／释放无线承载，
- 进行小区切换的过程
- 建立／更改／释放终端的测量和上报

对应于这些功能，RRC 连接重配置消息中可能携带的信息包括：

- 无线资源的配置情况（无线承载、MAC 层和物理层），其中含有如下信息：
  - 公共的配置信息，描述了移动性控制信息和系统信息中公共的无线资源配置情况，例如随机接入信道的参数、系统上行 sounding 导频配置和上行功率控制参数等静态的物理层配置信息
  - 专用的配置信息，专用的配置信息用于无线承载的管理，例如` MAC 层`配置、`SPS 调度`以及专用的物理层配置等
- 移动性控制的相关参数
- 无线测量的配置信息

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_25.webp" caption="RRC 连接重配置的过程">}}

按照 `RRC 连接重配置`消息中携带的配置信息，无线承载建立的过程包括建立一个 `PDCP 实体`、建立一个 `RLC 实体`，以及按照指定的逻辑信道标识建立一个逻辑信道。

与建立的过程相对应，当 `RRC 连接重配置`消息指示释放某个无线承载的时候，将释放相应的 `PDCP 实体`、`RLC 实体`和`逻辑信道`。通过 `RRC 连接`释放的过程，终端由 `RRC 连接状态`转变为`空闲状态`。这个过程释放 `RRC 连接`，包括所有已经建立的信令和数据的无线承载，以及所有的无线资源。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_26.webp" caption="RRC连接释放的过程">}}

#### 通信安全的功能

为了保证通信的安全，防止窃听、伪装等恶意行为，无线接入网的连接控制中包含安全通信的相关功能，包括：

- 信令消息的完整性保护
- 信令消息加密
- 数据消息的加密

在 `RRC` 连接建立的过程中，无线接入网从核心网取得终端用户的相关信息，然后可以使用安全模式激活的 RRC 消息设置用户密钥和加密算法，开启信令完整性保护和加密的功能，从而保证后续通信过程的安全（包括 `SRB2` 和 `DRB` 的建立等）。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_27.webp" caption="安全模式激活的过程">}}

在通信的过程中还可以通过计数器检查（counter check）的 `RRC 消息`防止`中间人`（man in the middle）攻击。在计数器检查的过程中，网络发送计数器检查消息要求终端确认在每个数据无线承载 `DRB` 上发送和接收数据的数量，通过这样的方法可以排除`中间人`可能进行的伪装通信。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_28.webp" caption="计数器检查的过程">}}

### 终端移动性的管理

终端移动性的管理是 LTE 作为移动通信系统一个重要的控制功能，实现相关功能的协议包括:

- 终端与无线接入网之间的 RRC 控制协议
- 终端与核心网之间非接入层（NAS）控制协议

#### 空闲状态终端的移动性管理

处于 `RRC 空闲状态`的终端与无线接入网没有建立 `RRC` 连接，终端的信息在核心网中注册并且分配有 `IP` 地址，无线接入网中不存储空闲状态终端的信息。终端进行自身的移动性管理，发起小区选择／重选，当跟踪区位置发生改变的时候向核心网进行登记。核心网记录终端所处的跟踪区位置（TrackingArea，TA，通常由相邻覆盖的若干个小区组成），核心网可以发起对终端的寻呼，终端根据核心网配置的 `DRX` 周期监听可能的寻呼消息。空闲状态的终端**不能进行**单播数据的传输。

在终端开机的过程中，首先根据 `PLMN`（公众网络标识）进行网络的选择，选定例如某个运营商的网络，之后终端进行小区选择，确定所驻留的小区，侦听该小区的控制信道。然后采用跟踪区改变的流程向核心网注册终端所处的跟踪区位置。

终端在移动的过程中，可能由一个小区无线信号的覆盖范围进入另一个小区的覆盖范围，这时候终端将进行小区重选的操作，改变所驻留侦听控制信道的小区。如果这个小区与前一个小区属于不同的跟踪区，那么在小区重选之后，终端将发起跟踪区改变的流程，使用非接入层（NAS）信令向核心网注册新的跟踪区位置。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_29.webp" caption="空闲状态终端的寻呼和跟踪区改变">}}

当有呼叫到达或者是系统消息改变需要向空闲状态的终端发送寻呼消息的时候，核心网`移动控制实体`（MME）根据终端所注册的跟踪区位置，找到相对应的跟踪区列表，然后在列表中所有的跟踪区上，发送针对该终端的寻呼消息。

#### 连接状态终端的移动性管理

处于 `RRC 连接状态`的终端与无线接入网建立了 `RRC` 连接，终端的信息在核心网和无线接入网中都进行存储和管理。网络登记终端所处的小区位置，终端的移动性由网络采用切换的流程进行管理。`RRC 连接状态`的终端**可以进行**单播数据的传输。为了节省耗电，终端可以采用由无线接入网配置的 `DRX` 功能。

`RRC 连接状态`终端的移动性由网络进行控制，网络根据无线接口的情况决定是否改变终端的服务小区，即切换的过程是由网络触发的。为了获取关于无线接口情况的信息，网络可以配置终端进行相关的测量和上报，然后根据上报的结果，触发切换的流程。另外，没有收到终端的测量与上报，网络也可以自行触发进行切换。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_30.webp" caption="连接状态终端的小区间切换">}}

##### 切换过程

首先，由源服务小区配置终端进行相关的测量和上报，根据上报的信息，源服务小区判断终端是否需要进行切换并且选择目标小区，触发切换的信令流程：

1. 通过 `eNodeB` 之间互联的 `X2` 接口，源小区向目标小区发送切换请求的消息，该消息中包含了对于目标小区无线资源需求情况的信息。
2. 如果目标小区确定可以接受终端进行切换，那么目标小区根据要求进行无线资源的准备，并向源小区反馈切换请求确认的消息，该消息包含终端接入目标小区时需要的信息，例如新的 `C-RNTI` 标识、目标小区的系统信息，以及分配的专用随机接入序列等。
3. 源服务小区在收到目标小区对于切换请求的确认消息后，采用带有移动性控制信息的 `RRC` 连接重配置消息将这些来自目标小区的接入配置信息转发给终端，并且命令终端向目标小区进行切换。(**注：** 此时，因为来自核心网数据的传输路由还没有发生改变，源基站还可能负责将数据转发给目标基站。)
4. 收到 RRC 连接重配置消息的切换命令后，终端根据指示的信息，向目标小区发起接入，采用随机接入的过程获得与目标小区的上行同步以及上行资源的分配。然后，在所分配的上行无线资源上，终端向目标小区发送 RRC 连接重配置完成消息，确认终端已经完成了切换，目标基站已经成为终端的服务基站，可以开始向终端发送数据。
5. 无线接入网的切换完成后，目标基站将向核心网的`移动控制实体`（MME）发送**路径切换请求**，并由 `MME` 协调数据网管（SGW）完成用户数据传输路径的改变，核心网将数据路径转移到目标小区。
6. 完成数据路径的转换后，目标小区向源小区发送终端上下文释放的消息，以此来确认成功地完成了整个切换的过程，源小区释放对应于该终端用户的相关资源。

#### 配置终端的测量和上报

为了进行移动性管理的操作，终端需要根据网络的配置对无线信道的情况进行测量，包括：

- 同频测量场景
- 异频测量场景
- 异系统的测量场景

网络可以通过广播或者专用的控制信息配置终端的测量，对于空闲状态的终端，通过网络广播消息中配置的测量参数，终端完成小区选择／重选等移动性功能。对于 `RRC 连接状态`的终端，通过 `RRC 信令`（即 `RRC 连接重配置消息`）可以对终端进行专门的测量配置，终端向网络上报测量结果，协助网络进行小区切换的移动性管理。

在进行测量时，如果终端的源服务小区与目标小区工作在相同的载波频率，那么称为`同频测量`，例如采用频率复用系数等于 1 的同频组网的情况。进行同频测量，**不需要测量间隔**，也就是说，在对同频目标小区进行测量的时候，终端**不需要中断**对源服务小区的信号接收。

源服务小区和目标小区工作在不同载波频率的场景属于`异频测量`，为了对异频的目标小区进行测量，通常情况下需要配置测量间隔，终端**中断**对源小区的信号接收，将接收频率调整到目标小区进行测量。

对终端的测量进行配置的消息内容主要包括`测量的对象`和`测量上报的配置`。其中，每一个测量的对象对应于 LTE 系统内同频或者异频的一个载波频率，或者某一个载波频率上异系统 WCDMA 的一个小区列表。而每一个测量上报的配置包括测量上报的格式，例如上报多少个目标小区的测量结果，以及上报的触发条件，包括周期性上报和事件触发两种可能性。

适用于 LTE 系统内同频或者异频的场景一共定义有 5 种事件，包括：

- A1 事件，服务小区的信号质量优于某个设定的门限
- A2 事件，服务小区的信号质量差于某个设定的门限
- A3 事件，相邻小区的信号质量优于服务小区超过某个设定的门限
- A4 事件，相邻小区的信号质量优于某个设定的门限
- A5，服务小区的信号质量差于某个设定的门限的同时，相邻小区的信号质量优于某个设定的门限

另外，针对与异系统之间的移动性操作，定义了两种事件：

- B1 事件，异系统相邻小区的信号质量优于某个设定的门限
- B2 事件，服务小区的信号质量差于某个设定的门限的同时，异系统相邻小区的信号质量优于某个设定的门限

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_31.webp" caption="终端测量上报的过程">}}

### 服务质量（QoS）管理

LTE 系统中采用 `EPS 承载`为单位进行端到端的服务质量（QoS）管理。

`EPS`（Evolved Packet System）承载是终端和分组数据网网关（P-GW）之间的连接，1 个 `EPS 承载`包括：
- 1 个无线承载，即终端和 eNodeB 基站之间的连接
- 1 个 S1 承载，即 eNodeB 和服务网关（S-GW）之间的连接
- 1 个 S5/S8 承载，即 S-GW 和 P-GW 之间的连接。

对于一个终端，网络可以建立**多个** `EPS 承载`，每个 `EPS 承载`可以有不同的 `QoS` 参数，因此除了在不同用户之间实现不同的 `QoS`，属于同一个用户的多个 `EPS 承载`也可以实现不同的 `QoS`，用于一个用户在同时进行的不同业务。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_32.webp" caption="LTE 系统中的承载">}}

针对每一个 `EPS 承载`，由核心网分配 `QoS 参数`，包括`QoS 类别标识`（QoS Class Identifier，QCI）和`分配和滞留优先级`（Allocation and Retention Priority，ARP）。

- QCI 参数描述了承载的 QoS 类别，每一种 QoS 类别都对应于一系列影响数据服务质量的具体系统参数，例如调度的加权、准入门限和排队门限，等等。
- ARP 参数描述了承载的优先级，包括在资源受限的情况下是否允许建立承载，或者是否丢弃某个优先级较低的承载

另外还有`保证数据速率`（Guaranteed Bit Rate，GBR）和`最大数据速率`（Maximum Bit Rate，MBR），这两个参数用于具有保证速率属性的承载。而对于不具有保证速率属性的承载，使用参数`最大总速率`（Aggregated Maximum Bit Rate，AMBR）来限制一个终端的所有不具有保证速率属性的承载的最大总速率。

根据核心网确定的 `QoS` 参数，无线接入网负责执行无线承载部分的 `QoS` 管理，例如无线资源的调度策略和排队管理策略等。

RRC 协议可以执行一部分 `QoS` 管理的功能，主要包括：半持续资源调度的配置，以及通过配置终端上行逻辑信道的优先级，实现对一个终端内多个上行承载的速率控制。

### 核心网信令的直接传输

除了 RRC 协议信息之外，RRC 消息还可以用于承载核心网的非接入层（NAS）信息。采用所定义的上行／下行 NAS 信息直接传输的 RRC 过程，可以通过`隧道打包`的方式，在终端用户和网络核心网之间传输 NAS 信息。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_32.webp" caption="上行（NAS）信息的直接传输">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_33.webp" caption="下行（NAS）信息的直接传输">}}

