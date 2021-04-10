# ESI

Defining ESI
E S I (Execute Scope Instances), is an Object Oriented Programming Model, in a simple and effective way, that meets all 5 S O L I D Principles.

Currently we have the most common structures such as Business (Business), Repository (Data Repository) and Entity (Data Type). Where Business invokes Repository, coupling everything in a Business Method. The Controller in turn calls Business to exposure in APIs, etc ...

See how it looks with ESI:

Business does not invoke Repository and vice versa. And you don't even need to, because the Controller is in charge of making this negotiation between the two layers;
In the Controller you " join " the Business x Repository Layers, in a simple and well structured way, where each Instance added in the Space is available for other Controllers and other Scopes, so on;
The Entity (Data Type) represented in the TData loaded in the ESIControllerBase is available and updated for each call of the ESIBusinessBase and ESIRepositoryBase Instances;
The System is completely decoupled, as each Instance of Business and Repository Classes does and performs a single, very specific function;
We have the base classes:

ESIBusinessBase <TData> - must implement the Abstract method protected void OnExecute (IESIConfigurationBusiness esiConfiguration);
ESIRepositoryBase <TData> - must implement the Void Abstract Method OnExecute (IESIConfigurationRepository esiConfiguration);
ESIGlobalBase <TData> - must contain methods with the void (IESIConfigurationBusiness esiConfiguration) and void (IESIConfigurationRepository) signatures;
ESIContollerBase <TData> - must implement the Void OnExecute Abstract Method (IESIConfigurationController esiConfiguration).
ESIBusinessBase<TData> example:
DTPInfoData is TData
using MMOJr.ESI.Business.Generic;
using MMOJr.ESI.Interface.Generic;

public class BUSInfoInstance : ESIBusinessBase<DTPInfoData>
{
    public BUSInfoInstance()
    {
    }

    protected override void OnExecute(IESIConfigurationBusiness<DTPInfoData> esiConfiguration)
    {
        esiConfiguration.Success = !string.IsNullOrEmpty(esiConfiguration.Data.DTPInfo.Instance);
        esiConfiguration.OnFailure = (p) =>
        {
            p.ListException.Add(new System.Exception("Instancia Inválida."));
        };
    }
}
ESIRepositoryBase<TData> example:
DTPInfoData is TData
using MMOJr.ESI.Interface.Generic;
using MMOJr.ESI.Repository.Generic;

public class DBSInfoLoadAssembly : ESIRepositoryBase<DTPInfoData>
{
    public DBSInfoLoadAssembly()
    {
    }

    protected override void OnExecute(IESIConfigurationRepository<DTPInfoData> esiConfiguration)
    {
        esiConfiguration.Data.Assembly = Assembly.LoadFile(esiConfiguration.Data.DTPInfo.PathAssembly);
    }
}
ESIController<TData> example:
DTPInfoData is TData
using MMOJr.ESI.Controller.Generic;
using MMOJr.ESI.Interface.Generic;

public class CTLInfoListMethodModuleAssembly : ESIControllerBase<DTPInfoData>
{
    public CTLInfoListMethodModuleAssembly(DTPInfo dtpInfo) : base(DTPInfoFactory.CreateInstanceInfoData(dtpInfo))
    {
    }

    protected override void OnExecute(IESIConfigurationController<DTPInfoData> esiConfiguration)
    {
        esiConfiguration.LoadScope(instance =>
        {
            instance.AddBusiness<BUSInfoListExceptionClear>();
            instance.AddBusiness<BUSInfoModuleAssembly>();
            instance.AddBusiness<BUSInfoPath>();
            instance.AddBusiness<BUSInfoNamespace>();
            instance.AddBusiness<BUSInfoPathAssembly>();
            instance.AddRepository<DBSInfoLoadAssembly>();
            instance.AddBusiness<BUSInfoAssemblyExistEndScope>();
            instance.AddRepository<DBSInfoMethodListByAssembly>();
            instance.AddBusiness<BUSInfoListExceptionEndExecution>();
        });
        esiConfiguration.LoadScope(instance =>
        {
            instance.AddBusiness<BUSInfoListExceptionClear>();
            instance.AddBusiness<BUSInfoModuleAssembly>();
            instance.AddBusiness<BUSInfoNamespace>();
            instance.AddBusiness<BUSInfoTypeName>();
            instance.AddBusiness<BUSInfoListExceptionThrow>();
            instance.AddRepository<DBSInfoLoadType>();
            instance.AddBusiness<BUSInfoTypeExist>();
            instance.AddBusiness<BUSInfoListExceptionThrow>();
            instance.AddRepository<DBSInfoMethodListByType>();
            instance.AddBusiness<BUSInfoListExceptionEndExecution>();
        });
    }
}
Call Controller:

 public static IESIReturn<DTPInfoData> ListMethod(DTPInfo dtpInfo)
 {
     var esiReturn = default(IESIReturn<DTPInfoData>);

     using (var ctlInfo = new CTLInfoListMethodModuleAssembly(dtpInfo))
     {
         esiReturn = ctlInfo.Execute();
     }

     return (esiReturn);
 }
