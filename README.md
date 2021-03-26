# ESI
Execute Scope Instances

E S I (Execute Scope Instances), é um Modelo de Programação Orientada a Objetos, de forma simples e eficaz, que atende a todos os 5 Princípios S O L I D.

Atualmente temos a estruturas mais comuns como Business (Negócio), Repository (Repositório de Dados) e Entity (Tipo de Dados). Onde a Business invoca Repository, acoplando tudo em um Método de Negócio. A Controller por sua vez chama a Business para exposição em APIs, etc....

Veja como fica com ESI:
* A Business não invoca Repository e vice-versa. E nem precisa, pois a Controller se encarrega de fazer esta negociação entre as duas camadas;
* Na Controller você "junta" as Camadas Business x Repository, de forma simples e bem estruturada, onde cada Instância adicionada no Espoco fica disponivel para outras Controllers e outros Escopos, assim por diante;
* A Entity (Tipo de Dados) representada na TData carregada na ESIControllerBase fica disponível e atualizada para cada chamada das Instâncias ESIBusinessBase e ESIRepositoryBase;
* O Sistema fica totalmente desacoplado, pois cada Instância de Classes Business e Repository fazem e executam uma única função bem específica.
