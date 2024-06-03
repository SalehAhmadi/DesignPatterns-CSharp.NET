using System;

namespace NullObjectPattern
{
  interface ILog
  {
    public void Warn();

    public static ILog Null => NullLog.Instance;

    private sealed class NullLog : ILog
    {
      private NullLog() {}

      private static Lazy<NullLog> instance =
        new Lazy<NullLog>(() => new NullLog());

      public static ILog Instance => instance.Value;

      public void Warn()
      {
        
      }
    }
  }

  public class BankAccount
  {
    public BankAccount(ILog log = ILog.Null)
    {
      
    }
  }

  class Program
  {
    static void Main(string[] args)
    {
      ILog log = ILog.Null;
    }
  }
}
