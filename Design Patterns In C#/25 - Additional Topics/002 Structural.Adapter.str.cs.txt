using System;
using System.Collections;
using System.Text;
using DotNetDesignPatternDemos.Annotations;
using NUnit.Framework;

namespace DotNetDesignPatternDemos.Structural.Adapter
{
  public class str : IEquatable<str>, IEquatable<string>
  {
    [NotNull] protected readonly byte[] buffer;

    public str()
    {
      buffer = new byte[]{};
    }
    
    public str(string s)
    {
      buffer = Encoding.ASCII.GetBytes(s);
    }
    
    protected str(byte[] buffer)
    {
      this.buffer = buffer;
    }

    public char this[int index]
    {
      get => (char) buffer[index];
      set => buffer[index] = (byte) value;
    }

    public bool Equals(str other)
    {
      if (other == null) return false;
      return ((IStructuralEquatable)buffer)
        .Equals(other.buffer, 
          StructuralComparisons.StructuralEqualityComparer);
    }

    public override bool Equals(object obj)
    {
      if (ReferenceEquals(null, obj)) return false;
      if (ReferenceEquals(this, obj)) return true;
      if (obj.GetType() != this.GetType()) return false;
      return Equals((str) obj);
    }

    public static bool operator ==(str left, str right)
    {
      return Equals(left, right);
    }

    public static bool operator !=(str left, str right)
    {
      return !Equals(left, right);
    }

    // state space explosion here
    public static str operator +(str first, str second)
    {
      byte[] bytes = new byte[first.buffer.Length + second.buffer.Length];
      first.buffer.CopyTo(bytes, 0);
      second.buffer.CopyTo(bytes, first.buffer.Length);
      return new str(bytes);
    }

    public override int GetHashCode()
    {
      return ToString().GetHashCode();
    }

    

    public static implicit operator str(string s)
    {
      return new str(s);
    }

    public bool Equals(string other)
    {
      return ToString().Equals(other);
    }

    public override string ToString()
    {
      return Encoding.ASCII.GetString(buffer);
    }
  }

  [TestFixture]
  class StrTests
  {
    string text = "testing!";
    
    [Test]
    public void CreationTest()
    {
      var constructed = new str(text);
      Assert.That(constructed.ToString(), Is.EqualTo(text));

      str assigned = text;
      Assert.That(assigned.ToString(), Is.EqualTo(text));
    }

    [Test]
    public void ComparisonTest()
    {
      str first = text;
      str second = text;
      
      // Equals
      Assert.That(first, Is.EqualTo(second));
      // op ==
      Assert.That(first == second);
      // op == with a C# string
      Assert.That(first == text);
      Assert.That(second, Is.EqualTo(text));
    }

    [Test]
    public void ConcatenationTest()
    {
      str foo = "foo";
      str bar = "bar";
      
      Assert.That(foo+bar, Is.EqualTo("foobar"));
      Assert.That("foo"+bar, Is.EqualTo("foobar"));
      Assert.That(foo+"bar", Is.EqualTo("foobar"));

      foo += bar;
      Assert.That(foo, Is.EqualTo("foobar"));
    }
  }
}