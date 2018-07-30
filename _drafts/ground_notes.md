## Version Graphs

We begin with the version graph layer of Common Ground, which captures changes corresponding to the C in the ABCs of data context. This layer bootstraps the representation of all information in Ground, by providing the classes upon which all other layers are based. 

我们从Common Ground的Version graph开始，在数据上下文的ABC模型中，对应c这一层，用来捕捉变化

---

Ground links Versions into VersionHistoryDAGs via VersionSuccessor edges indicating that one version is the descendant of another (the short dark edges in the bottom of the figure.)

ground通过VersionSuccessor边来连接不同的Version形成VersionHistoryDAG

---

Type parametrization ensures that all of the VersionSuccessors in a given DAG link the same subclass of Versions together. 

类型参数化确保在给出的DAG图(有向无环图)中的VersionSuccessors连接相同Version的子类。

---

External Items and Schrödinger Versioning

在ground中这里提到是ground可以追踪不放在ground中的数据，比如github的仓库信息和google docs这样的在ground之外管理的数据，ground也可以追踪记录

---

## Model Graphs

The model graph level of Common Ground provides a model-agnostic representation of application metadata: the A of our ABCs.We use a graph model for flexibility: graphs can repre-sent metadata entities and relationships from semistructured (JSON, XML) and structured (Relational, Object-Oriented, Matrix) data models. 

这里大致意思应该是说这一层是ABC模型的A，通过使用graph model来表示数据模型，包括结构化的数据或者半结构化的数据。

---


