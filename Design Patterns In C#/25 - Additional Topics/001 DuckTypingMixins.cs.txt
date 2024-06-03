using System;
using System.Collections;
using System.Collections.Generic;
using System.Xml;

namespace Demo
{
  interface IScalar<T> : IEnumerable<T>
  {
    IEnumerator<T> IEnumerable<T>.GetEnumerator()
    {
      yield return (T) this;
    }

    IEnumerator IEnumerable.GetEnumerator() => GetEnumerator();
  }

  public class MyClass : IScalar<MyClass>
  {
    public override string ToString()
    {
      return "MyClass";
    }
  }
  
  public class Demo
  {
    public static void Main(string[] args)
    {
      // duck typing
      
      // GetEnumerator() — foreach (IEnumerable<T>)
      // Dispose() — using (IDisposable)

      // mixin

      var mc = new MyClass();
      foreach (var x in mc)
        Console.WriteLine(x);
    }
  }
}