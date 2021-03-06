

### eureka和zookeeper的区别

CAP理论指出，一个分布式系统不可能同时满足C（consistency 一致性）、A（availability 可用性）和P（partition tolerance 分区容错性）。由于分区容错性在分布式系统中是必须要保证的，因此我们只能在A和C之间进行权衡。

当向注册中心查询服务列表时，我们可以容忍注册中心返回的是几分钟以前的注册信息，但不能接受服务直接down掉不可用。也就是说，<u>服务注册功能对可用性的要求要高于一致性</u>。 

Eureka(CA)：可以在发生因网络问题导致的各节点失去联系时也不会暂停服务，但是最新的数据可能不统一。

Zookeeper(CP)：如果发生网络问题导致 Master和其他节点失去联系，就会使得其他的节点推选出新的 Master，但是推选的时间内无法提供服务；可以保证任何时候的数据都是统一的。 

## 服务架构概念解析

### 分布式服务架构

<u>把一个完整的系统按照业务功能拆分成一个个独立的子系统</u>，每个子系统就被称为"服务"。单独为某一节点添加服务器（不同模块部署在不同服务器上 )，需系统之间配合才能完成整个业务逻辑。在分布式结构中，<u>这些子系统能够独立运行在web容器中，它们之间可以通过RPC进行通信</u>。

作用：解决网站高并发带来问题

- 系统之间的<u>耦合度大大降低</u>，可以独立开发、独立部署、独立测试，系统与系统之间的边界非常明确，排错也变得相当容易，开发效率大大提升。
- 系统耦合度降低从而<u>更易于扩展</u>，我们可以针对性地扩展某些服务。（假设这个商城要搞一次大促，下单量可能会大大提升，我们可以针对性地提升订单系统、产品系统的节点数量，而对于后台管理系统、数据分析系统而言，节点数量维持原有水平即可。）
- <u>服务的复用性更高</u>。（比如，当我们将用户系统作为单独的服务后，该公司所有的产品都可以使用该系统作为用户系统，无需重复开发。）

**集群**

同一个业务，部署在多个服务器上。通过负载均衡设备共同对外提供服务；各服务可独立应用，组合服务也可系统应用。<u>分布式中的每一个节点，都可以做集群。 而集群并不一定就是分布式的</u>。 

优势： 高性能、高可用、高性价比、可伸缩性

### **面向服务架构**

又称 SOA ：Service Oriented Architecture 

面向服务的架构是一种软件体系结构，应用程序的不同组件通过网络上的通信协议向其他组件提供服务。面向服务的架构不太关心如何对应用程序进行模块化构建，更多的是关心如何通过分布式、单独维护和部署的软件组件的集成来组成应用程序。

在分布式架构基础上再次拆分为服务层、表现层；业务系统分解为多个组件，让每个组件都独立提供离散，自治，可复用的服务能力，<u>通过服务的组合和编排来实现上层的业务流程</u> 。

优势：简化维护，降低整体风险，伸缩灵活

### **微服务架构**

微服务架构在某种程度上是SOA继续发展的下一步。

<u>这些服务的创建仅限于一个特定的业务功能</u>，如用户管理、用户角色、电子商务车、搜索引擎、社交媒体登录等。此外，<u>它们是完全独立的，也就是说它们可以写入不同的编程语言并使用不同的数据库</u>。集中式服务管理几乎不存在，微服务使用轻量级HTTP、REST或Thrift API进行通信。

**分布式架构与微服务架构的区别：**

- <u>分布式架构侧重于机器隔离，微服务架构侧重于微小服务、进程隔离；</u>
- <u>分布式架构强调的是服务的分散化，微服务架构则更强调服务的专业化；</u>

既没有规模又不需要太多变化的业务，如果采用微服务架构改造，引入各种复杂性，比如部署工作量的增加、复杂链路的监控难题，这就是为微服务而微服务，只会得不偿失。

**SOA与微服务架构的区别：**

- 通信 - 在SOA中，由于每个服务都通过ESB进行通信，如果其中一个服务变慢，可能会阻塞ESB，ESB可能成为影响整个系统的单一故障点。另一方面，微服务在容错方面要好得多，如果一个微服务存在内存错误，那么只有该微服务会受到影响，所有其他微服务将继续处理请求。
- 互操作性 - SOA通过消息中间件促进了多种异构协议的使用。微服务试图通过减少集成选择的数量来简化架构模式。因此，如果您想要在异构环境中使用不同协议来集成多个系统，则需要考虑SOA。如果您的所有服务都可以通过相同的远程访问协议访问，那么微服务对您来说是一个更好的选择。
- 大小Size - SOA和微服务的主要区别在于规模和范围。微服务架构中的前缀“微”是指内部组件的粒度，意味着它们必须比SOA架构的服务往往要小得多。<u>微服务中的服务组件通常有一个单一的目的，而SOA服务中通常包含更多的业务功能，并且通常将它们实现为完整的子系统。</u>

