# Anti-Idle App with .NET
Prevent being idle in Microsoft Teams


## csproj

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net8.0</TargetFramework>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
    <AllowUnsafeBlocks>true</AllowUnsafeBlocks>
  </PropertyGroup>

</Project>
```

## Program.cs

```csharp
using System.Runtime.InteropServices;

namespace BenjaminAbt.AntiIdleApp;

internal partial class Program
{
    // Import the Windows API functions to control the mouse position
    [LibraryImport("user32.dll")]
    [return: MarshalAs(UnmanagedType.Bool)]
    public static partial bool SetCursorPos(int X, int Y);

    [LibraryImport("user32.dll")]
    [return: MarshalAs(UnmanagedType.Bool)]
    public static partial bool GetCursorPos(out POINT lpPoint);

    // Structure to store the mouse position
    public struct POINT
    {
        public int X;
        public int Y;
    }

    public static async Task Main(string[] args)
    {
        Console.WriteLine("Anti Idle is running. Press CTRL+C to exit.");

        // Create a PeriodicTimer that ticks every 1 minute (60 seconds)
        using PeriodicTimer timer = new(TimeSpan.FromMinutes(1));

        // Loop that waits for each tick of the timer
        while (await timer.WaitForNextTickAsync())
        {
            // Get the current mouse position
            if (GetCursorPos(out POINT currentPos))
            {
                // Move the mouse cursor 1 pixel to the right
                SetCursorPos(currentPos.X + 1, currentPos.Y);

                // Short delay to ensure the movement is registered
                await Task.Delay(50);

                // Move the mouse back to the original position
                SetCursorPos(currentPos.X, currentPos.Y);
            }
        }
    }
}

```

Enjoy.
