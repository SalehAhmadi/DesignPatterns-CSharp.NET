using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Configuration;

namespace ConsoleDemo
{
  public static class ExtensionMethods
  {
    public struct BoolMarker<T>
    {
      public bool Result;
      public T Self;

      public enum Operation
      {
        None,
        And,
        Or
      };

      internal Operation PendingOp;

      internal BoolMarker(bool result, T self, Operation pendingOp)
      {
        Result = result;
        Self = self;
        PendingOp = pendingOp;
      }

      public BoolMarker(bool result, T self)
        : this(result, self, Operation.None)
      {
      }

      public BoolMarker<T> And => new BoolMarker<T>(Result, Self, Operation.And);

      public static implicit operator bool(BoolMarker<T> marker)
      {
        return marker.Result;
      }
    }

    public static T AddTo<T>(this T self, params ICollection<T>[] colls)
    {
      foreach (var coll in colls)
        coll.Add(self);
      return self;
    }

    public static bool IsOneOf<T>(this T self, params T[] values)
    {
      return values.Contains(self);
    }

    public static BoolMarker<TSubject> HasNo<TSubject, T>(this TSubject self,
      Func<TSubject, IEnumerable<T>> props)
    {
      return new BoolMarker<TSubject>(!props(self).Any(), self);
    }

    public static BoolMarker<TSubject> HasSome<TSubject, T>(this TSubject self,
      Func<TSubject, IEnumerable<T>> props)
    {
      return new BoolMarker<TSubject>(props(self).Any(),self);
    }

    public static BoolMarker<T> HasNo<T, U>(this BoolMarker<T> marker,
      Func<T, IEnumerable<U>> props)
    {
      if (marker.PendingOp == BoolMarker<T>.Operation.And && !marker.Result)
        return marker;
      return new BoolMarker<T>(!props(marker.Self).Any(), marker.Self);
    }
  }

  public class Person
  {
    public List<string> Names = new List<string>();
    public List<Person> Children = new List<Person>();
  }

  public class MyClass
  {
    public void AddingNumbers()
    {
      var list = new List<int>();
      var list2 = new List<int>();
      //list.Add(24);
      24.AddTo(list,list2);
    }

    public void ProcessCommand(string opcode)
    {
      //if (opcode == "AND" || opcode == "OR" || opcode == "XOR") {}
      //if (new[]{"AND", "OR", "XOR"}.Contains(opcode)) {}
      //if ("AND OR XOR".Split(' ').Contains(opcode)) {}
      if (opcode.IsOneOf("AND", "OR", "XOR")) {}
    }

    public void Process(Person person)
    {
      //if (person.Names.Count == 0) {}
      //if (!person.Names.Any())
      if (person.HasSome(p => p.Names).And.HasNo(p => p.Children)) {}
    }
  }
}