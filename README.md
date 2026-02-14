# Guía Completa de C# 2026 - Clean Code & Mejores Prácticas

## 📑 Índice

### 1. Fundamentos
- [1.1 Tipos de Datos](#11-tipos-de-datos)
- [1.2 Variables y Constantes](#12-variables-y-constantes)
- [1.3 Conversiones](#13-conversiones)
- [1.4 Operadores](#14-operadores)

### 2. Control de Flujo
- [2.1 Condicionales](#21-condicionales)
- [2.2 Bucles](#22-bucles)
- [2.3 Pattern Matching (C# 12+)](#23-pattern-matching)

### 3. Estructuras de Datos
- [3.1 Arrays](#31-arrays)
- [3.2 Listas y Colecciones](#32-listas-y-colecciones)
- [3.3 Diccionarios](#33-diccionarios)
- [3.4 LINQ Esencial](#34-linq-esencial)

### 4. Métodos y Funciones
- [4.1 Sintaxis de Métodos](#41-sintaxis-de-métodos)
- [4.2 Expresiones Lambda](#42-expresiones-lambda)
- [4.3 Métodos de Extensión](#43-métodos-de-extensión)
- [4.4 Delegates y Events](#44-delegates-y-events)

### 5. Programación Orientada a Objetos
- [5.1 Clases y Objetos](#51-clases-y-objetos)
- [5.2 Propiedades Modernas](#52-propiedades-modernas)
- [5.3 Herencia y Polimorfismo](#53-herencia-y-polimorfismo)
- [5.4 Interfaces](#54-interfaces)

### 6. Características Modernas C# 12/13
- [6.1 Primary Constructors](#61-primary-constructors)
- [6.2 Collection Expressions](#62-collection-expressions)
- [6.3 Raw String Literals](#63-raw-string-literals)
- [6.4 Required Members](#64-required-members)

### 7. Manejo de Errores
- [7.1 Try-Catch-Finally](#71-try-catch-finally)
- [7.2 Excepciones Personalizadas](#72-excepciones-personalizadas)
- [7.3 Validaciones](#73-validaciones)

### 8. Asincronía
- [8.1 async/await](#81-asyncawait)
- [8.2 Task y Task<T>](#82-task-y-taskt)
- [8.3 Mejores Prácticas Async](#83-mejores-prácticas-async)

### 9. Null Safety
- [9.1 Nullable Reference Types](#91-nullable-reference-types)
- [9.2 Null-Coalescing Operators](#92-null-coalescing-operators)
- [9.3 Null-Conditional Operator](#93-null-conditional-operator)

### 10. Clean Code en C#
- [10.1 Nomenclatura](#101-nomenclatura)
- [10.2 Métodos Limpios](#102-métodos-limpios)
- [10.3 SOLID Principles](#103-solid-principles)
- [10.4 Refactoring Común](#104-refactoring-común)

### 11. Patrones de Diseño Prácticos
- [11.1 Repository Pattern](#111-repository-pattern)
- [11.2 Dependency Injection](#112-dependency-injection)
- [11.3 Builder Pattern](#113-builder-pattern)

### 12. Testing
- [12.1 Unit Testing Básico](#121-unit-testing-básico)
- [12.2 Mocking](#122-mocking)

---

## 1. Fundamentos

### 1.1 Tipos de Datos

```csharp
// Tipos numéricos
int edad = 25;                    // -2,147,483,648 a 2,147,483,647
long poblacion = 8_000_000_000L;  // Separadores numéricos (C# 7+)
double distancia = 384_400.0;     // Ciencia/matemáticas
decimal precio = 19.99m;          // Dinero (precisión exacta)
float temperatura = 36.5f;        // 32 bits

// Cadenas y caracteres
string nombre = "César";          // Inmutable
char inicial = 'C';

// Booleanos
bool esActivo = true;

// DateTime moderno
DateTime ahora = DateTime.Now;
DateOnly fecha = new(2026, 2, 3);  // C# 10+
TimeOnly hora = new(14, 30);       // C# 10+
```

**🎯 Clean Code:**
```csharp
// ❌ MAL - Tipos ambiguos
var x = 10;
var y = "texto";

// ✅ BIEN - Tipos claros cuando no es obvio
int cantidadProductos = 10;
string nombreUsuario = "texto";

// ✅ BIEN - var cuando el tipo es obvio
var cliente = new Cliente();
var precios = new List<decimal>();
```

### 1.2 Variables y Constantes

```csharp
// Constantes
private const double PI = 3.14159;
private const string NOMBRE_APP = "MiApp";
private const int MAXIMO_INTENTOS = 3;

// Variables de solo lectura (asignadas en constructor)
private readonly DateTime fechaCreacion;

// C# 12: Inline arrays para rendimiento
[InlineArray(10)]
public struct Buffer
{
    private int _element0;
}
```

**🎯 Clean Code:**
```csharp
// ❌ MAL - Magic numbers
if (edad > 18) { }
decimal total = precio * 1.18;

// ✅ BIEN - Constantes con nombres descriptivos
private const int EDAD_MINIMA_ADULTO = 18;
private const decimal TASA_IGV = 1.18m;

if (edad > EDAD_MINIMA_ADULTO) { }
decimal total = precio * TASA_IGV;
```

### 1.3 Conversiones

```csharp
// Conversión implícita (segura)
int numero = 10;
double resultado = numero;

// Conversión explícita (cast)
double precio = 19.99;
int precioEntero = (int)precio; // Trunca: 19

// Parse - lanza excepción
int numero1 = int.Parse("123");

// TryParse - forma segura (RECOMENDADO)
if (int.TryParse(textBox.Text, out int resultado))
{
    Console.WriteLine($"Número válido: {resultado}");
}
else
{
    Console.WriteLine("Entrada inválida");
}

// Convert (maneja null)
int? valorNullable = null;
int valor = Convert.ToInt32(valorNullable); // 0 si es null

// ToString con formato
decimal precio = 1234.56m;
string precioFormateado = precio.ToString("C", 
    new CultureInfo("es-PE")); // S/1,234.56
```

**🎯 Clean Code:**
```csharp
// ❌ MAL - Parse sin validación
private void CalcularTotal()
{
    int cantidad = int.Parse(txtCantidad.Text); // Puede explotar
    decimal precio = decimal.Parse(txtPrecio.Text);
}

// ✅ BIEN - Validación adecuada
private bool CalcularTotal(out decimal total)
{
    total = 0;
    
    if (!int.TryParse(txtCantidad.Text, out int cantidad) || cantidad <= 0)
    {
        MostrarError("Cantidad inválida");
        return false;
    }
    
    if (!decimal.TryParse(txtPrecio.Text, out decimal precio) || precio <= 0)
    {
        MostrarError("Precio inválido");
        return false;
    }
    
    total = cantidad * precio;
    return true;
}
```

### 1.4 Operadores

```csharp
// Aritméticos
int suma = 10 + 5;
int division = 10 / 3;        // 3 (división entera)
double divisionReal = 10.0 / 3; // 3.333...
int modulo = 10 % 3;          // 1 (resto)

// Incremento/Decremento
int x = 5;
x++;      // Post-incremento: usa luego incrementa
++x;      // Pre-incremento: incrementa luego usa
x += 10;  // Atajo: x = x + 10

// Comparación
bool igual = (5 == 5);
bool diferente = (5 != 3);
bool mayor = (10 > 5);

// Lógicos
bool and = true && false;  // false
bool or = true || false;   // true
bool not = !true;          // false

// Operador Elvis (null-coalescing)
string nombre = nombreUsuario ?? "Invitado";

// Null-conditional
int? longitud = texto?.Length; // null si texto es null

// Pattern matching operator
bool esAdulto = edad is >= 18 and < 65;
```

---

## 2. Control de Flujo

### 2.1 Condicionales

```csharp
// if-else tradicional
if (edad >= 18)
{
    Console.WriteLine("Mayor de edad");
}
else if (edad >= 13)
{
    Console.WriteLine("Adolescente");
}
else
{
    Console.WriteLine("Niño");
}

// Operador ternario
string categoria = edad >= 18 ? "Adulto" : "Menor";

// Switch tradicional
switch (dia)
{
    case 1:
        Console.WriteLine("Lunes");
        break;
    case 2:
        Console.WriteLine("Martes");
        break;
    default:
        Console.WriteLine("Otro día");
        break;
}

// Switch expression (C# 8+) - MODERNO
string nombreDia = dia switch
{
    1 => "Lunes",
    2 => "Martes",
    3 => "Miércoles",
    4 => "Jueves",
    5 => "Viernes",
    6 or 7 => "Fin de semana",
    _ => "Día inválido"
};

// Switch con when (condiciones)
string DescribirTemperatura(int temp) => temp switch
{
    < 0 => "Congelado",
    >= 0 and < 15 => "Frío",
    >= 15 and < 25 => "Agradable",
    >= 25 and < 35 => "Caliente",
    >= 35 => "Muy caliente",
    _ => "Desconocido"
};
```

**🎯 Clean Code:**
```csharp
// ❌ MAL - Ternario anidado
string mensaje = edad >= 18 
    ? genero == "M" 
        ? "Sr." 
        : "Sra." 
    : genero == "M" 
        ? "Joven" 
        : "Jovencita";

// ✅ BIEN - Switch expression legible
string ObtenerTratamiento(int edad, string genero) => (edad, genero) switch
{
    (>= 18, "M") => "Sr.",
    (>= 18, "F") => "Sra.",
    (< 18, "M") => "Joven",
    (< 18, "F") => "Jovencita",
    _ => "Persona"
};
```

### 2.2 Bucles

```csharp
// for clásico
for (int i = 0; i < 10; i++)
{
    Console.WriteLine(i);
}

// for con Range (C# 8+)
for (int i = 0; i < 10; i++)
{
    Console.WriteLine(i);
}

// foreach - el más usado
var nombres = new[] { "Ana", "Luis", "Pedro" };
foreach (var nombre in nombres)
{
    Console.WriteLine(nombre);
}

// foreach con índice (C# 12+)
foreach (var (item, index) in nombres.Index())
{
    Console.WriteLine($"{index}: {item}");
}

// while
int contador = 0;
while (contador < 10)
{
    Console.WriteLine(contador);
    contador++;
}

// do-while (ejecuta al menos una vez)
do
{
    Console.WriteLine("Ingrese opción:");
} while (opcion != "salir");
```

**🎯 Clean Code:**
```csharp
// ❌ MAL - Bucle con mucha lógica
for (int i = 0; i < productos.Count; i++)
{
    if (productos[i].Precio > 100 && productos[i].Stock > 0)
    {
        decimal descuento = productos[i].Precio * 0.1m;
        productos[i].Precio -= descuento;
        // ... más lógica
    }
}

// ✅ BIEN - Lógica extraída a métodos
foreach (var producto in productos.Where(p => p.EsElegibleParaDescuento()))
{
    producto.AplicarDescuento(0.1m);
}

// Método de extensión o en la clase Producto
public bool EsElegibleParaDescuento() => Precio > 100 && Stock > 0;
public void AplicarDescuento(decimal porcentaje) => Precio *= (1 - porcentaje);
```

### 2.3 Pattern Matching (C# 12+)

```csharp
// Type pattern
object obj = "Hola";
if (obj is string texto)
{
    Console.WriteLine(texto.ToUpper());
}

// Property pattern
public record Persona(string Nombre, int Edad);

if (persona is { Edad: >= 18, Nombre: var nombre })
{
    Console.WriteLine($"{nombre} es adulto");
}

// List patterns (C# 11+)
int[] numeros = { 1, 2, 3, 4 };
if (numeros is [1, 2, .., var ultimo])
{
    Console.WriteLine($"Último: {ultimo}"); // 4
}

// Switch con pattern
string Clasificar(object obj) => obj switch
{
    null => "Nulo",
    int n when n < 0 => "Negativo",
    int n when n == 0 => "Cero",
    int n => "Positivo",
    string s when string.IsNullOrEmpty(s) => "Cadena vacía",
    string s => $"Texto: {s}",
    _ => "Desconocido"
};
```

---

## 3. Estructuras de Datos

### 3.1 Arrays

```csharp
// Declaración
int[] numeros = new int[5];
string[] nombres = { "Ana", "Luis", "Pedro" };

// Array multidimensional
int[,] matriz = new int[3, 3];
matriz[0, 0] = 1;

// Array jagged (arrays de arrays)
int[][] jagged = new int[3][];
jagged[0] = new int[] { 1, 2, 3 };

// Ranges y indices (C# 8+)
int[] arr = { 1, 2, 3, 4, 5 };
int[] primerosTres = arr[..3];      // { 1, 2, 3 }
int[] ultimosTres = arr[^3..];      // { 3, 4, 5 }
int ultimo = arr[^1];               // 5
```

### 3.2 Listas y Colecciones

```csharp
// List<T> - la más usada
List<int> numeros = new();           // C# 9+
numeros.Add(10);
numeros.AddRange(new[] { 20, 30 });
numeros.Remove(10);
numeros.RemoveAt(0);

// Collection expression (C# 12)
List<int> nums = [1, 2, 3, 4, 5];

// Stack (LIFO)
Stack<string> pila = new();
pila.Push("primero");
string elemento = pila.Pop();

// Queue (FIFO)
Queue<string> cola = new();
cola.Enqueue("primero");
string primero = cola.Dequeue();

// HashSet (único, no ordenado)
HashSet<int> unicos = new() { 1, 2, 3 };
unicos.Add(1); // No se duplica

// LinkedList (inserción/eliminación eficiente)
LinkedList<string> lista = new();
lista.AddFirst("inicio");
lista.AddLast("fin");
```

**🎯 Clean Code:**
```csharp
// ❌ MAL - Nombre genérico
List<string> lista = new();
List<int> datos = new();

// ✅ BIEN - Nombres descriptivos
List<string> nombresClientes = new();
List<int> idsProductosSeleccionados = new();

// ❌ MAL - Modificar lista mientras se itera
foreach (var item in lista)
{
    if (condicion)
        lista.Remove(item); // ¡Excepción!
}

// ✅ BIEN - Usar RemoveAll o crear nueva lista
lista.RemoveAll(item => condicion);
// o
var listaFiltrada = lista.Where(item => !condicion).ToList();
```

### 3.3 Diccionarios

```csharp
// Dictionary<TKey, TValue>
Dictionary<string, int> edades = new()
{
    ["Ana"] = 25,
    ["Luis"] = 30
};

// Agregar
edades.Add("Pedro", 28);
edades["María"] = 22; // Sobrescribe si existe

// Leer de forma segura
if (edades.TryGetValue("Ana", out int edad))
{
    Console.WriteLine($"Edad de Ana: {edad}");
}

// ContainsKey
if (edades.ContainsKey("Luis"))
{
    Console.WriteLine("Luis existe");
}

// Iterar
foreach (var (nombre, edad) in edades)
{
    Console.WriteLine($"{nombre}: {edad}");
}
```

**🎯 Clean Code:**
```csharp
// ❌ MAL - Acceso directo sin validar
int edad = diccionario["clave"]; // KeyNotFoundException si no existe

// ✅ BIEN - TryGetValue
if (diccionario.TryGetValue("clave", out int edad))
{
    // Usar edad
}

// ✅ MEJOR - Null-coalescing
int edad = diccionario.TryGetValue("clave", out int e) ? e : 0;

// ✅ C# 11+ - GetValueOrDefault
int edad = diccionario.GetValueOrDefault("clave", 0);
```

### 3.4 LINQ Esencial

```csharp
var numeros = new[] { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };

// Where (filtrar)
var pares = numeros.Where(n => n % 2 == 0);

// Select (transformar)
var cuadrados = numeros.Select(n => n * n);

// OrderBy / OrderByDescending
var ordenados = numeros.OrderByDescending(n => n);

// First / FirstOrDefault
int primero = numeros.First(n => n > 5);        // 6 (excepción si no existe)
int? primeroONulo = numeros.FirstOrDefault(n => n > 100); // null

// Any / All
bool hayPares = numeros.Any(n => n % 2 == 0);
bool todosMayoresCero = numeros.All(n => n > 0);

// Count
int cantidad = numeros.Count(n => n > 5);

// Sum / Average / Min / Max
int suma = numeros.Sum();
double promedio = numeros.Average();
int minimo = numeros.Min();
int maximo = numeros.Max();

// Take / Skip
var primerosCinco = numeros.Take(5);
var saltarPrimeros3 = numeros.Skip(3);

// Distinct
var unicos = new[] { 1, 2, 2, 3, 3, 4 }.Distinct();

// GroupBy
var productos = new[]
{
    new { Nombre = "Laptop", Categoria = "Tech" },
    new { Nombre = "Mouse", Categoria = "Tech" },
    new { Nombre = "Silla", Categoria = "Mueble" }
};

var porCategoria = productos.GroupBy(p => p.Categoria);

// Chunk (C# 11 - dividir en grupos)
var grupos = numeros.Chunk(3); // [[1,2,3], [4,5,6], [7,8,9], [10]]
```

**🎯 Clean Code LINQ:**
```csharp
// ❌ MAL - Múltiples iteraciones
var lista = productos.Where(p => p.Precio > 100).ToList();
lista = lista.OrderBy(p => p.Nombre).ToList();
lista = lista.Take(10).ToList();

// ✅ BIEN - Una sola cadena
var lista = productos
    .Where(p => p.Precio > 100)
    .OrderBy(p => p.Nombre)
    .Take(10)
    .ToList();

// ❌ MAL - FirstOrDefault sin null-check
var producto = productos.FirstOrDefault(p => p.Id == id);
string nombre = producto.Nombre; // NullReferenceException

// ✅ BIEN - Con null-conditional
var nombre = productos.FirstOrDefault(p => p.Id == id)?.Nombre;

// ✅ C# 12+ - Collection expressions con LINQ
List<int> paresMayores = [..numeros.Where(n => n % 2 == 0 && n > 5)];
```

---

## 4. Métodos y Funciones

### 4.1 Sintaxis de Métodos

```csharp
// Método tradicional
private int Sumar(int a, int b)
{
    return a + b;
}

// Expression body (una línea)
private int Sumar(int a, int b) => a + b;

// Método void
private void MostrarMensaje(string texto)
{
    MessageBox.Show(texto);
}

// Método void expression body
private void Log(string mensaje) => Console.WriteLine($"[LOG] {mensaje}");

// Parámetros opcionales
private void Saludar(string nombre, string saludo = "Hola")
{
    Console.WriteLine($"{saludo}, {nombre}");
}

// Parámetros nombrados
Saludar(nombre: "Ana", saludo: "Buenos días");

// Parámetros ref y out
private void Intercambiar(ref int a, ref int b)
{
    int temp = a;
    a = b;
    b = temp;
}

// out parameters
private bool DividirConResto(int dividendo, int divisor, out int cociente, out int resto)
{
    if (divisor == 0)
    {
        cociente = 0;
        resto = 0;
        return false;
    }
    
    cociente = dividendo / divisor;
    resto = dividendo % divisor;
    return true;
}

// params (número variable de argumentos)
private int Sumar(params int[] numeros) => numeros.Sum();

// Llamada: Sumar(1, 2, 3, 4, 5)
```

**🎯 Clean Code:**
```csharp
// ❌ MAL - Método que hace demasiado
private void ProcesarPedido(Pedido pedido)
{
    // Validar
    if (pedido == null) throw new ArgumentNullException();
    if (pedido.Items.Count == 0) throw new InvalidOperationException();
    
    // Calcular
    decimal total = 0;
    foreach (var item in pedido.Items)
    {
        total += item.Precio * item.Cantidad;
    }
    
    // Aplicar descuento
    if (pedido.Cliente.EsPremium)
        total *= 0.9m;
    
    // Guardar
    database.Guardar(pedido);
    
    // Enviar email
    emailService.Enviar(pedido.Cliente.Email, "Pedido confirmado");
}

// ✅ BIEN - Principio de Responsabilidad Única
private void ProcesarPedido(Pedido pedido)
{
    ValidarPedido(pedido);
    
    decimal total = CalcularTotal(pedido);
    total = AplicarDescuentos(total, pedido.Cliente);
    
    pedido.Total = total;
    GuardarPedido(pedido);
    EnviarConfirmacion(pedido);
}

private void ValidarPedido(Pedido pedido)
{
    ArgumentNullException.ThrowIfNull(pedido);
    
    if (pedido.Items.Count == 0)
        throw new InvalidOperationException("El pedido debe tener al menos un item");
}

private decimal CalcularTotal(Pedido pedido) =>
    pedido.Items.Sum(item => item.Precio * item.Cantidad);

private decimal AplicarDescuentos(decimal total, Cliente cliente) =>
    cliente.EsPremium ? total * 0.9m : total;
```

### 4.2 Expresiones Lambda

```csharp
// Lambda básica
Func<int, int> cuadrado = x => x * x;

// Lambda con múltiples parámetros
Func<int, int, int> suma = (a, b) => a + b;

// Lambda con bloque
Func<int, int, int> dividir = (a, b) =>
{
    if (b == 0) throw new DivideByZeroException();
    return a / b;
};

// Action (no retorna valor)
Action<string> log = mensaje => Console.WriteLine($"[LOG] {mensaje}");

// Predicate (retorna bool)
Predicate<int> esPar = n => n % 2 == 0;

// Uso con LINQ
var mayores = numeros.Where(n => n > 5);
var dobles = numeros.Select(n => n * 2);
```

### 4.3 Métodos de Extensión

```csharp
// Definir método de extensión
public static class StringExtensions
{
    public static bool EsEmailValido(this string email)
    {
        if (string.IsNullOrWhiteSpace(email))
            return false;
        
        return email.Contains("@") && email.Contains(".");
    }
    
    public static string Truncar(this string texto, int longitudMaxima)
    {
        if (texto.Length <= longitudMaxima)
            return texto;
        
        return texto[..longitudMaxima] + "...";
    }
}

// Uso
string email = "usuario@ejemplo.com";
bool esValido = email.EsEmailValido();

string textoLargo = "Este es un texto muy largo";
string corto = textoLargo.Truncar(10); // "Este es un..."
```

**🎯 Clean Code - Extensiones útiles:**
```csharp
public static class LinqExtensions
{
    // ForEach para IEnumerable
    public static void ForEach<T>(this IEnumerable<T> source, Action<T> action)
    {
        foreach (var item in source)
            action(item);
    }
    
    // IsNullOrEmpty para IEnumerable
    public static bool IsNullOrEmpty<T>(this IEnumerable<T>? source)
    {
        return source == null || !source.Any();
    }
}

public static class DecimalExtensions
{
    public static string AMoneda(this decimal valor, string cultura = "es-PE")
    {
        return valor.ToString("C", new CultureInfo(cultura));
    }
}

// Uso
List<string> nombres = ["Ana", "Luis"];
nombres.ForEach(n => Console.WriteLine(n));

decimal precio = 100.50m;
string texto = precio.AMoneda(); // "S/ 100.50"
```

### 4.4 Delegates y Events

```csharp
// Delegate
public delegate void NotificacionHandler(string mensaje);

public class Notificador
{
    public event NotificacionHandler? OnNotificacion;
    
    public void Notificar(string mensaje)
    {
        OnNotificacion?.Invoke(mensaje);
    }
}

// Uso
var notificador = new Notificador();
notificador.OnNotificacion += mensaje => Console.WriteLine($"Notificación: {mensaje}");
notificador.Notificar("Hola mundo");

// EventHandler<T> moderno
public class PedidoProcesadoEventArgs : EventArgs
{
    public int PedidoId { get; init; }
    public decimal Total { get; init; }
}

public class ProcesadorPedidos
{
    public event EventHandler<PedidoProcesadoEventArgs>? PedidoProcesado;
    
    public void Procesar(Pedido pedido)
    {
        // Procesar...
        
        PedidoProcesado?.Invoke(this, new PedidoProcesadoEventArgs
        {
            PedidoId = pedido.Id,
            Total = pedido.Total
        });
    }
}
```

---

## 5. Programación Orientada a Objetos

### 5.1 Clases y Objetos

```csharp
// Clase básica
public class Persona
{
    // Campos privados
    private string nombre;
    private int edad;
    
    // Constructor
    public Persona(string nombre, int edad)
    {
        this.nombre = nombre;
        this.edad = edad;
    }
    
    // Métodos
    public void Presentarse()
    {
        Console.WriteLine($"Hola, soy {nombre} y tengo {edad} años");
    }
}

// Uso
var persona = new Persona("Ana", 25);
persona.Presentarse();
```

### 5.2 Propiedades Modernas

```csharp
// Auto-properties
public class Producto
{
    public int Id { get; set; }
    public string Nombre { get; set; }
    public decimal Precio { get; set; }
}

// Init-only properties (C# 9)
public class ProductoInmutable
{
    public int Id { get; init; }
    public string Nombre { get; init; }
    public decimal Precio { get; init; }
}

var producto = new ProductoInmutable
{
    Id = 1,
    Nombre = "Laptop",
    Precio = 1500
};
// producto.Precio = 1000; // Error de compilación

// Required properties (C# 11)
public class Cliente
{
    public required string Nombre { get; init; }
    public required string Email { get; init; }
    public string? Telefono { get; init; }
}

// Debe inicializar propiedades requeridas
var cliente = new Cliente
{
    Nombre = "Ana",
    Email = "ana@example.com"
};

// Property con lógica
public class CuentaBancaria
{
    private decimal saldo;
    
    public decimal Saldo
    {
        get => saldo;
        set
        {
            if (value < 0)
                throw new ArgumentException("El saldo no puede ser negativo");
            saldo = value;
        }
    }
}

// Expression-bodied property
public class Rectangulo
{
    public double Ancho { get; set; }
    public double Alto { get; set; }
    
    public double Area => Ancho * Alto;
    public double Perimetro => 2 * (Ancho + Alto);
}
```

**🎯 Clean Code OOP:**
```csharp
// ❌ MAL - Clase anémica (solo getters/setters)
public class Pedido
{
    public decimal Subtotal { get; set; }
    public decimal Descuento { get; set; }
    public decimal Impuesto { get; set; }
    public decimal Total { get; set; }
}

// Lógica en otro lugar
decimal CalcularTotal(Pedido pedido)
{
    return pedido.Subtotal - pedido.Descuento + pedido.Impuesto;
}

// ✅ BIEN - Comportamiento dentro de la clase
public class Pedido
{
    public decimal Subtotal { get; private set; }
    public decimal Descuento { get; private set; }
    public decimal Impuesto { get; private set; }
    
    public decimal Total => Subtotal - Descuento + Impuesto;
    
    public void AplicarDescuento(decimal porcentaje)
    {
        if (porcentaje < 0 || porcentaje > 1)
            throw new ArgumentException("Descuento debe estar entre 0 y 1");
        
        Descuento = Subtotal * porcentaje;
    }
    
    public void CalcularImpuesto(decimal tasa)
    {
        Impuesto = Subtotal * tasa;
    }
}
```

### 5.3 Herencia y Polimorfismo

```csharp
// Clase base
public abstract class Empleado
{
    public string Nombre { get; init; }
    public decimal SalarioBase { get; init; }
    
    public abstract decimal CalcularSalario();
    
    public virtual void Presentarse()
    {
        Console.WriteLine($"Hola, soy {Nombre}");
    }
}

// Clases derivadas
public class EmpleadoTiempoCompleto : Empleado
{
    public override decimal CalcularSalario()
    {
        return SalarioBase;
    }
}

public class EmpleadoPorHoras : Empleado
{
    public int HorasTrabajadas { get; init; }
    
    public override decimal CalcularSalario()
    {
        return SalarioBase * HorasTrabajadas;
    }
    
    public override void Presentarse()
    {
        base.Presentarse();
        Console.WriteLine($"Trabajo {HorasTrabajadas} horas");
    }
}

// Sealed class (no se puede heredar)
public sealed class EmpleadoCEO : Empleado
{
    public override decimal CalcularSalario()
    {
        return SalarioBase * 10;
    }
}
```

### 5.4 Interfaces

```csharp
// Interfaz
public interface IRepositorio<T>
{
    void Agregar(T entidad);
    void Actualizar(T entidad);
    void Eliminar(int id);
    T? ObtenerPorId(int id);
    IEnumerable<T> ObtenerTodos();
}

// Implementación
public class RepositorioProductos : IRepositorio<Producto>
{
    private readonly List<Producto> productos = new();
    
    public void Agregar(Producto entidad)
    {
        productos.Add(entidad);
    }
    
    public void Actualizar(Producto entidad)
    {
        var indice = productos.FindIndex(p => p.Id == entidad.Id);
        if (indice >= 0)
            productos[indice] = entidad;
    }
    
    public void Eliminar(int id)
    {
        productos.RemoveAll(p => p.Id == id);
    }
    
    public Producto? ObtenerPorId(int id)
    {
        return productos.FirstOrDefault(p => p.Id == id);
    }
    
    public IEnumerable<Producto> ObtenerTodos()
    {
        return productos;
    }
}

// Default interface methods (C# 8+)
public interface ILogger
{
    void Log(string mensaje);
    
    void LogError(string error)
    {
        Log($"ERROR: {error}");
    }
    
    void LogInfo(string info)
    {
        Log($"INFO: {info}");
    }
}
```

---

## 6. Características Modernas C# 12/13

### 6.1 Primary Constructors

```csharp
// Antes (C# 11)
public class Producto
{
    public int Id { get; init; }
    public string Nombre { get; init; }
    
    public Producto(int id, string nombre)
    {
        Id = id;
        Nombre = nombre;
    }
}

// C# 12 - Primary Constructor
public class Producto(int id, string nombre)
{
    public int Id { get; init; } = id;
    public string Nombre { get; init; } = nombre;
    
    public void MostrarInfo()
    {
        Console.WriteLine($"{id}: {nombre}"); // Acceso directo a parámetros
    }
}

// Uso con dependency injection
public class ServicioProductos(IRepositorio<Producto> repositorio, ILogger logger)
{
    public void CrearProducto(Producto producto)
    {
        logger.Log($"Creando producto: {producto.Nombre}");
        repositorio.Agregar(producto);
    }
}
```

### 6.2 Collection Expressions

```csharp
// C# 12 - Sintaxis unificada para colecciones
int[] array = [1, 2, 3, 4, 5];
List<string> lista = ["Ana", "Luis", "Pedro"];
Span<int> span = [10, 20, 30];

// Spread operator
int[] primeros = [1, 2, 3];
int[] segundos = [4, 5, 6];
int[] combinados = [..primeros, ..segundos]; // [1,2,3,4,5,6]

// Con LINQ
List<int> pares = [..numeros.Where(n => n % 2 == 0)];

// Empty collections
List<string> vacia = [];
```

### 6.3 Raw String Literals

```csharp
// C# 11+ - Raw strings para JSON, XML, etc.
string json = """
    {
        "nombre": "Ana",
        "edad": 25,
        "activo": true
    }
    """;

// Interpolación en raw strings
string nombre = "Ana";
int edad = 25;
string personaJson = $$"""
    {
        "nombre": "{{nombre}}",
        "edad": {{edad}}
    }
    """;

// SQL con raw strings
string sql = """
    SELECT *
    FROM Productos
    WHERE Precio > 100
        AND Stock > 0
    ORDER BY Nombre
    """;
```

### 6.4 Required Members

```csharp
// Propiedades requeridas (C# 11)
public class ConfiguracionApp
{
    public required string CadenaConexion { get; init; }
    public required string ApiKey { get; init; }
    public int TimeoutSegundos { get; init; } = 30;
}

// El compilador fuerza la inicialización
var config = new ConfiguracionApp
{
    CadenaConexion = "...",
    ApiKey = "..."
    // TimeoutSegundos es opcional
};
```

---

## 7. Manejo de Errores

### 7.1 Try-Catch-Finally

```csharp
try
{
    int resultado = Dividir(10, 0);
}
catch (DivideByZeroException ex)
{
    Console.WriteLine($"Error: {ex.Message}");
}
catch (Exception ex)
{
    Console.WriteLine($"Error inesperado: {ex.Message}");
}
finally
{
    // Siempre se ejecuta
    Console.WriteLine("Limpieza");
}

// Exception filters (C# 6+)
try
{
    // código
}
catch (HttpRequestException ex) when (ex.StatusCode == HttpStatusCode.NotFound)
{
    Console.WriteLine("Recurso no encontrado");
}
catch (HttpRequestException ex) when (ex.StatusCode == HttpStatusCode.Unauthorized)
{
    Console.WriteLine("No autorizado");
}
```

**🎯 Clean Code:**
```csharp
// ❌ MAL - Tragarse excepciones
try
{
    GuardarDatos();
}
catch
{
    // No hace nada
}

// ❌ MAL - Catch genérico innecesario
try
{
    var resultado = int.Parse(texto);
}
catch (Exception ex)
{
    // Demasiado genérico
}

// ✅ BIEN - Catch específico
try
{
    var resultado = int.Parse(texto);
}
catch (FormatException)
{
    MostrarError("Formato numérico inválido");
}
catch (OverflowException)
{
    MostrarError("Número demasiado grande");
}

// ✅ MEJOR - Evitar try-catch con validación
if (int.TryParse(texto, out int resultado))
{
    // Usar resultado
}
else
{
    MostrarError("Formato inválido");
}
```

### 7.2 Excepciones Personalizadas

```csharp
public class ProductoNoEncontradoException : Exception
{
    public int ProductoId { get; }
    
    public ProductoNoEncontradoException(int productoId)
        : base($"Producto con ID {productoId} no encontrado")
    {
        ProductoId = productoId;
    }
    
    public ProductoNoEncontradoException(int productoId, Exception inner)
        : base($"Producto con ID {productoId} no encontrado", inner)
    {
        ProductoId = productoId;
    }
}

// Uso
public Producto ObtenerProducto(int id)
{
    var producto = repositorio.ObtenerPorId(id);
    
    if (producto == null)
        throw new ProductoNoEncontradoException(id);
    
    return producto;
}
```

### 7.3 Validaciones

```csharp
// ArgumentNullException helper (C# 11+)
public void ProcesarDatos(string datos)
{
    ArgumentNullException.ThrowIfNull(datos);
    ArgumentException.ThrowIfNullOrWhiteSpace(datos);
    
    // Procesar...
}

// Validación de rangos (C# 11+)
public void EstablecerEdad(int edad)
{
    ArgumentOutOfRangeException.ThrowIfNegative(edad);
    ArgumentOutOfRangeException.ThrowIfGreaterThan(edad, 150);
    
    // Asignar...
}

// Guard clauses
public decimal CalcularDescuento(Cliente cliente, decimal monto)
{
    ArgumentNullException.ThrowIfNull(cliente);
    
    if (monto <= 0)
        throw new ArgumentException("El monto debe ser positivo", nameof(monto));
    
    if (!cliente.EsActivo)
        return 0;
    
    if (cliente.EsPremium)
        return monto * 0.2m;
    
    return monto * 0.1m;
}
```

---

## 8. Asincronía

### 8.1 async/await

```csharp
// Método asíncrono básico
public async Task<string> ObtenerDatosAsync()
{
    await Task.Delay(1000); // Simula operación lenta
    return "Datos obtenidos";
}

// Uso
string datos = await ObtenerDatosAsync();

// Método async void (solo para event handlers)
private async void Button_Click(object sender, EventArgs e)
{
    string datos = await ObtenerDatosAsync();
    MessageBox.Show(datos);
}

// Múltiples tareas en paralelo
public async Task<(string Usuario, string Productos)> ObtenerDatosParaleloAsync()
{
    var tareaUsuario = ObtenerUsuarioAsync();
    var tareaProductos = ObtenerProductosAsync();
    
    await Task.WhenAll(tareaUsuario, tareaProductos);
    
    return (tareaUsuario.Result, tareaProductos.Result);
}
```

### 8.2 Task y Task<T>

```csharp
// Crear Task
Task tarea = Task.Run(() =>
{
    // Trabajo pesado
    Thread.Sleep(2000);
});

// Task con resultado
Task<int> tareaConResultado = Task.Run(() =>
{
    Thread.Sleep(1000);
    return 42;
});

int resultado = await tareaConResultado;

// ValueTask para evitar allocations
public async ValueTask<int> ObtenerCacheadoAsync(string clave)
{
    if (cache.ContainsKey(clave))
        return cache[clave]; // No crea Task
    
    return await ObtenerDeDatabaseAsync(clave);
}
```

### 8.3 Mejores Prácticas Async

```csharp
// ✅ BIEN - Nombres terminan en Async
public async Task<List<Producto>> ObtenerProductosAsync()
{
    // ...
}

// ✅ BIEN - ConfigureAwait en librerías
public async Task<string> ObtenerDatosAsync()
{
    var response = await httpClient.GetAsync(url).ConfigureAwait(false);
    return await response.Content.ReadAsStringAsync().ConfigureAwait(false);
}

// ❌ MAL - async void (excepto event handlers)
public async void GuardarDatos() // No hacer esto
{
    await repositorio.GuardarAsync();
}

// ✅ BIEN - async Task
public async Task GuardarDatosAsync()
{
    await repositorio.GuardarAsync();
}

// ✅ BIEN - Manejo de errores
public async Task<Result> ProcesarAsync()
{
    try
    {
        var datos = await ObtenerDatosAsync();
        return Result.Success(datos);
    }
    catch (HttpRequestException ex)
    {
        return Result.Failure(ex.Message);
    }
}

// ❌ MAL - Bloquear código async
var resultado = ObtenerDatosAsync().Result; // ¡Deadlock!
ObtenerDatosAsync().Wait(); // ¡Deadlock!

// ✅ BIEN - Await
var resultado = await ObtenerDatosAsync();
```

---

## 9. Null Safety

### 9.1 Nullable Reference Types

```csharp
// Activar en .csproj
// <Nullable>enable</Nullable>

// Tipos nullables
string? nombreOpcional = null;  // Puede ser null
string nombreRequerido = "Ana"; // No puede ser null

// El compilador advierte
string nombre = null; // Warning: Converting null literal

public class Cliente
{
    public required string Nombre { get; init; }     // No-nullable
    public string? Apellido { get; init; }           // Nullable
    public required string Email { get; init; }      // No-nullable
}

// Null-forgiving operator (!)
string? texto = ObtenerTexto();
int longitud = texto!.Length; // "Confío en que no es null"
```

### 9.2 Null-Coalescing Operators

```csharp
// ?? (null-coalescing)
string nombre = nombreUsuario ?? "Invitado";

// ??= (null-coalescing assignment)
string? cache = null;
cache ??= ObtenerDatos(); // Solo asigna si cache es null

// Encadenamiento
string resultado = primero ?? segundo ?? tercero ?? "default";
```

### 9.3 Null-Conditional Operator

```csharp
// ?. (null-conditional)
int? longitud = texto?.Length;

// Encadenamiento
string? ciudad = persona?.Direccion?.Ciudad;

// Con índices
char? primerCaracter = texto?[0];

// Con métodos
string? mayusculas = texto?.ToUpper();

// Con eventos
OnCambio?.Invoke(this, EventArgs.Empty);

// Combinación con null-coalescing
int longitud = texto?.Length ?? 0;
```

**🎯 Clean Code - Null Safety:**
```csharp
// ❌ MAL - Múltiples null checks
public decimal CalcularDescuento(Cliente cliente)
{
    if (cliente == null)
        return 0;
    
    if (cliente.Membresia == null)
        return 0;
    
    if (cliente.Membresia.Descuento == null)
        return 0;
    
    return cliente.Membresia.Descuento.Porcentaje;
}

// ✅ BIEN - Null-conditional
public decimal CalcularDescuento(Cliente? cliente)
{
    return cliente?.Membresia?.Descuento?.Porcentaje ?? 0;
}

// ✅ MEJOR - Guard clause
public decimal CalcularDescuento(Cliente cliente)
{
    ArgumentNullException.ThrowIfNull(cliente);
    
    var descuento = cliente.Membresia?.Descuento;
    return descuento?.Porcentaje ?? 0;
}
```

---

## 10. Clean Code en C#

### 10.1 Nomenclatura

```csharp
// ✅ CLASES: PascalCase
public class ProductoServicio { }
public class RepositorioCliente { }

// ✅ INTERFACES: I + PascalCase
public interface IRepositorio { }
public interface IEmailService { }

// ✅ MÉTODOS: PascalCase, verbos
public void GuardarProducto() { }
public bool ValidarEmail() { }
public List<Cliente> ObtenerClientesActivos() { }

// ✅ PROPIEDADES: PascalCase
public string NombreCompleto { get; set; }
public int CantidadTotal { get; set; }

// ✅ VARIABLES LOCALES: camelCase
int cantidadProductos = 10;
string nombreCliente = "Ana";

// ✅ PARÁMETROS: camelCase
public void Procesar(int idProducto, string nombreUsuario) { }

// ✅ CAMPOS PRIVADOS: _camelCase (opcional)
private readonly ILogger _logger;
private int _contador;

// ✅ CONSTANTES: PascalCase o MAYUSCULAS
private const int MaximoIntentos = 3;
private const string RUTA_ARCHIVO = "data.txt";

// ❌ MAL - Nombres no descriptivos
var d = DateTime.Now;
var temp = ObtenerDatos();
public void Proc() { }

// ✅ BIEN - Nombres descriptivos
var fechaActual = DateTime.Now;
var clientesActivos = ObtenerDatos();
public void ProcesarPedido() { }
```

### 10.2 Métodos Limpios

```csharp
// ❌ MAL - Método largo con múltiples responsabilidades
public void ProcesarVenta(int clienteId, List<int> productosIds)
{
    // Validar cliente
    var cliente = db.Clientes.Find(clienteId);
    if (cliente == null) throw new Exception("Cliente no existe");
    if (!cliente.Activo) throw new Exception("Cliente inactivo");
    
    // Obtener productos
    var productos = new List<Producto>();
    foreach (var id in productosIds)
    {
        var p = db.Productos.Find(id);
        if (p != null && p.Stock > 0)
            productos.Add(p);
    }
    
    // Calcular total
    decimal total = 0;
    foreach (var p in productos)
    {
        total += p.Precio;
        p.Stock--;
    }
    
    // Aplicar descuento
    if (cliente.EsPremium)
        total *= 0.9m;
    
    // Guardar venta
    var venta = new Venta
    {
        ClienteId = clienteId,
        Total = total,
        Fecha = DateTime.Now
    };
    db.Ventas.Add(venta);
    db.SaveChanges();
    
    // Enviar email
    emailService.Enviar(cliente.Email, "Venta procesada");
}

// ✅ BIEN - Métodos pequeños y enfocados
public async Task ProcesarVentaAsync(int clienteId, List<int> productosIds)
{
    var cliente = await ValidarClienteAsync(clienteId);
    var productos = await ObtenerProductosDisponiblesAsync(productosIds);
    
    var total = CalcularTotal(productos);
    total = AplicarDescuento(total, cliente);
    
    await ActualizarStockAsync(productos);
    var venta = await GuardarVentaAsync(clienteId, productos, total);
    await EnviarConfirmacionAsync(cliente, venta);
}

private async Task<Cliente> ValidarClienteAsync(int clienteId)
{
    var cliente = await db.Clientes.FindAsync(clienteId);
    
    if (cliente == null)
        throw new ClienteNoEncontradoException(clienteId);
    
    if (!cliente.Activo)
        throw new ClienteInactivoException(clienteId);
    
    return cliente;
}

private async Task<List<Producto>> ObtenerProductosDisponiblesAsync(List<int> productosIds)
{
    var productos = await db.Productos
        .Where(p => productosIds.Contains(p.Id) && p.Stock > 0)
        .ToListAsync();
    
    return productos;
}

private decimal CalcularTotal(List<Producto> productos) =>
    productos.Sum(p => p.Precio);

private decimal AplicarDescuento(decimal total, Cliente cliente) =>
    cliente.EsPremium ? total * 0.9m : total;
```

### 10.3 SOLID Principles

```csharp
// S - Single Responsibility Principle
// ❌ MAL
public class Usuario
{
    public void GuardarEnBaseDatos() { }
    public void EnviarEmail() { }
    public void ValidarDatos() { }
}

// ✅ BIEN
public class Usuario
{
    public string Nombre { get; init; }
    public string Email { get; init; }
}

public class RepositorioUsuarios
{
    public void Guardar(Usuario usuario) { }
}

public class ServicioEmail
{
    public void Enviar(string destinatario, string mensaje) { }
}

public class ValidadorUsuario
{
    public bool Validar(Usuario usuario) { }
}

// O - Open/Closed Principle
// ❌ MAL
public class CalculadorDescuento
{
    public decimal Calcular(Cliente cliente, decimal monto)
    {
        if (cliente.Tipo == "Premium")
            return monto * 0.2m;
        else if (cliente.Tipo == "Gold")
            return monto * 0.15m;
        else
            return monto * 0.1m;
    }
}

// ✅ BIEN
public interface IEstrategiaDescuento
{
    decimal CalcularDescuento(decimal monto);
}

public class DescuentoPremium : IEstrategiaDescuento
{
    public decimal CalcularDescuento(decimal monto) => monto * 0.2m;
}

public class DescuentoGold : IEstrategiaDescuento
{
    public decimal CalcularDescuento(decimal monto) => monto * 0.15m;
}

public class DescuentoRegular : IEstrategiaDescuento
{
    public decimal CalcularDescuento(decimal monto) => monto * 0.1m;
}

public class Cliente
{
    public required IEstrategiaDescuento EstrategiaDescuento { get; init; }
    
    public decimal CalcularDescuento(decimal monto) =>
        EstrategiaDescuento.CalcularDescuento(monto);
}

// L - Liskov Substitution Principle
// ❌ MAL
public class Ave
{
    public virtual void Volar() { }
}

public class Pinguino : Ave
{
    public override void Volar()
    {
        throw new NotSupportedException("Los pingüinos no vuelan");
    }
}

// ✅ BIEN
public interface IAve { }

public interface IVolador
{
    void Volar();
}

public class Aguila : IAve, IVolador
{
    public void Volar() { }
}

public class Pinguino : IAve
{
    // No implementa IVolador
}

// I - Interface Segregation Principle
// ❌ MAL
public interface ITrabajador
{
    void Trabajar();
    void Comer();
    void Dormir();
    void ProgramarReunion();
}

// ✅ BIEN
public interface ITrabajador
{
    void Trabajar();
}

public interface IPersona
{
    void Comer();
    void Dormir();
}

public interface IAdministrador
{
    void ProgramarReunion();
}

// D - Dependency Inversion Principle
// ❌ MAL
public class ServicioClientes
{
    private readonly SqlDatabase database = new();
    
    public void Guardar(Cliente cliente)
    {
        database.Insert(cliente);
    }
}

// ✅ BIEN
public interface IRepositorio<T>
{
    void Agregar(T entidad);
}

public class ServicioClientes
{
    private readonly IRepositorio<Cliente> _repositorio;
    
    public ServicioClientes(IRepositorio<Cliente> repositorio)
    {
        _repositorio = repositorio;
    }
    
    public void Guardar(Cliente cliente)
    {
        _repositorio.Agregar(cliente);
    }
}
```

### 10.4 Refactoring Común

```csharp
// 1. Extract Method
// ❌ ANTES
public void ProcesarPedido(Pedido pedido)
{
    decimal total = 0;
    foreach (var item in pedido.Items)
    {
        total += item.Precio * item.Cantidad;
    }
    
    if (pedido.Cliente.EsPremium)
    {
        total *= 0.9m;
    }
    
    pedido.Total = total;
}

// ✅ DESPUÉS
public void ProcesarPedido(Pedido pedido)
{
    var subtotal = CalcularSubtotal(pedido.Items);
    var total = AplicarDescuentos(subtotal, pedido.Cliente);
    pedido.Total = total;
}

private decimal CalcularSubtotal(List<Item> items) =>
    items.Sum(i => i.Precio * i.Cantidad);

private decimal AplicarDescuentos(decimal subtotal, Cliente cliente) =>
    cliente.EsPremium ? subtotal * 0.9m : subtotal;

// 2. Replace Magic Numbers
// ❌ ANTES
if (edad > 18 && edad < 65)
{
    precio *= 0.85m;
}

// ✅ DESPUÉS
private const int EDAD_MINIMA_ADULTO = 18;
private const int EDAD_MAXIMA_ADULTO = 65;
private const decimal DESCUENTO_ADULTO = 0.85m;

if (edad > EDAD_MINIMA_ADULTO && edad < EDAD_MAXIMA_ADULTO)
{
    precio *= DESCUENTO_ADULTO;
}

// 3. Replace Conditional with Polymorphism
// ❌ ANTES
public decimal CalcularSalario(Empleado emp)
{
    if (emp.Tipo == "Gerente")
        return emp.SalarioBase * 1.5m;
    else if (emp.Tipo == "Desarrollador")
        return emp.SalarioBase * 1.2m;
    else
        return emp.SalarioBase;
}

// ✅ DESPUÉS
public abstract class Empleado
{
    public decimal SalarioBase { get; init; }
    public abstract decimal CalcularSalario();
}

public class Gerente : Empleado
{
    public override decimal CalcularSalario() => SalarioBase * 1.5m;
}

public class Desarrollador : Empleado
{
    public override decimal CalcularSalario() => SalarioBase * 1.2m;
}
```

---

## 11. Patrones de Diseño Prácticos

### 11.1 Repository Pattern

```csharp
// Interfaz
public interface IRepositorio<T> where T : class
{
    Task<T?> ObtenerPorIdAsync(int id);
    Task<IEnumerable<T>> ObtenerTodosAsync();
    Task AgregarAsync(T entidad);
    Task ActualizarAsync(T entidad);
    Task EliminarAsync(int id);
}

// Implementación
public class RepositorioGenerico<T> : IRepositorio<T> where T : class
{
    private readonly DbContext _context;
    private readonly DbSet<T> _dbSet;
    
    public RepositorioGenerico(DbContext context)
    {
        _context = context;
        _dbSet = context.Set<T>();
    }
    
    public async Task<T?> ObtenerPorIdAsync(int id)
    {
        return await _dbSet.FindAsync(id);
    }
    
    public async Task<IEnumerable<T>> ObtenerTodosAsync()
    {
        return await _dbSet.ToListAsync();
    }
    
    public async Task AgregarAsync(T entidad)
    {
        await _dbSet.AddAsync(entidad);
        await _context.SaveChangesAsync();
    }
    
    public async Task ActualizarAsync(T entidad)
    {
        _dbSet.Update(entidad);
        await _context.SaveChangesAsync();
    }
    
    public async Task EliminarAsync(int id)
    {
        var entidad = await ObtenerPorIdAsync(id);
        if (entidad != null)
        {
            _dbSet.Remove(entidad);
            await _context.SaveChangesAsync();
        }
    }
}
```

### 11.2 Dependency Injection

```csharp
// Configuración (Program.cs)
var builder = WebApplication.CreateBuilder(args);

// Registro de servicios
builder.Services.AddScoped<IRepositorio<Producto>, RepositorioProductos>();
builder.Services.AddScoped<IProductoService, ProductoService>();
builder.Services.AddSingleton<ICacheService, CacheService>();
builder.Services.AddTransient<IEmailService, EmailService>();

var app = builder.Build();

// Uso en controladores
public class ProductosController : ControllerBase
{
    private readonly IProductoService _productoService;
    private readonly ILogger<ProductosController> _logger;
    
    public ProductosController(
        IProductoService productoService,
        ILogger<ProductosController> logger)
    {
        _productoService = productoService;
        _logger = logger;
    }
    
    [HttpGet]
    public async Task<IActionResult> ObtenerTodos()
    {
        try
        {
            var productos = await _productoService.ObtenerTodosAsync();
            return Ok(productos);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error al obtener productos");
            return StatusCode(500, "Error interno");
        }
    }
}
```

### 11.3 Builder Pattern

```csharp
// Sin Builder (muchos parámetros)
var pedido = new Pedido(
    cliente: cliente,
    items: items,
    direccion: direccion,
    metodoPago: "Tarjeta",
    aplicarDescuento: true,
    esUrgente: false,
    notasEspeciales: "Entregar antes de las 5pm"
);

// Con Builder
public class PedidoBuilder
{
    private Cliente _cliente;
    private List<Item> _items = new();
    private Direccion? _direccion;
    private string _metodoPago = "Efectivo";
    private bool _aplicarDescuento;
    private bool _esUrgente;
    private string? _notasEspeciales;
    
    public PedidoBuilder ParaCliente(Cliente cliente)
    {
        _cliente = cliente;
        return this;
    }
    
    public PedidoBuilder AgregarItem(Item item)
    {
        _items.Add(item);
        return this;
    }
    
    public PedidoBuilder ConDireccion(Direccion direccion)
    {
        _direccion = direccion;
        return this;
    }
    
    public PedidoBuilder ConMetodoPago(string metodoPago)
    {
        _metodoPago = metodoPago;
        return this;
    }
    
    public PedidoBuilder ConDescuento()
    {
        _aplicarDescuento = true;
        return this;
    }
    
    public PedidoBuilder Urgente()
    {
        _esUrgente = true;
        return this;
    }
    
    public PedidoBuilder ConNotas(string notas)
    {
        _notasEspeciales = notas;
        return this;
    }
    
    public Pedido Build()
    {
        // Validaciones
        ArgumentNullException.ThrowIfNull(_cliente);
        
        if (_items.Count == 0)
            throw new InvalidOperationException("El pedido debe tener al menos un item");
        
        return new Pedido
        {
            Cliente = _cliente,
            Items = _items,
            Direccion = _direccion ?? _cliente.DireccionPrincipal,
            MetodoPago = _metodoPago,
            AplicarDescuento = _aplicarDescuento,
            EsUrgente = _esUrgente,
            NotasEspeciales = _notasEspeciales
        };
    }
}

// Uso
var pedido = new PedidoBuilder()
    .ParaCliente(cliente)
    .AgregarItem(item1)
    .AgregarItem(item2)
    .ConMetodoPago("Tarjeta")
    .ConDescuento()
    .ConNotas("Entregar antes de las 5pm")
    .Build();
```

---

## 12. Testing

### 12.1 Unit Testing Básico

```csharp
// Usando xUnit
public class CalculadoraTests
{
    [Fact]
    public void Sumar_DosNumerosPositivos_RetornaSuma()
    {
        // Arrange
        var calculadora = new Calculadora();
        
        // Act
        var resultado = calculadora.Sumar(5, 3);
        
        // Assert
        Assert.Equal(8, resultado);
    }
    
    [Theory]
    [InlineData(1, 2, 3)]
    [InlineData(5, 5, 10)]
    [InlineData(-1, 1, 0)]
    public void Sumar_VariosNumeros_RetornaResultadoCorrecto(
        int a, int b, int esperado)
    {
        // Arrange
        var calculadora = new Calculadora();
        
        // Act
        var resultado = calculadora.Sumar(a, b);
        
        // Assert
        Assert.Equal(esperado, resultado);
    }
    
    [Fact]
    public void Dividir_DivisorCero_LanzaExcepcion()
    {
        // Arrange
        var calculadora = new Calculadora();
        
        // Act & Assert
        Assert.Throws<DivideByZeroException>(() =>
            calculadora.Dividir(10, 0));
    }
}

// Testing async
public class ProductoServiceTests
{
    [Fact]
    public async Task ObtenerPorId_ProductoExiste_RetornaProducto()
    {
        // Arrange
        var mockRepo = new Mock<IRepositorio<Producto>>();
        var productoEsperado = new Producto { Id = 1, Nombre = "Test" };
        
        mockRepo
            .Setup(r => r.ObtenerPorIdAsync(1))
            .ReturnsAsync(productoEsperado);
        
        var service = new ProductoService(mockRepo.Object);
        
        // Act
        var resultado = await service.ObtenerPorIdAsync(1);
        
        // Assert
        Assert.NotNull(resultado);
        Assert.Equal(1, resultado.Id);
        Assert.Equal("Test", resultado.Nombre);
    }
}
```

### 12.2 Mocking

```csharp
// Usando Moq
public class PedidoServiceTests
{
    private readonly Mock<IRepositorio<Pedido>> _mockRepo;
    private readonly Mock<IEmailService> _mockEmail;
    private readonly PedidoService _service;
    
    public PedidoServiceTests()
    {
        _mockRepo = new Mock<IRepositorio<Pedido>>();
        _mockEmail = new Mock<IEmailService>();
        _service = new PedidoService(_mockRepo.Object, _mockEmail.Object);
    }
    
    [Fact]
    public async Task CrearPedido_DatosValidos_GuardaYEnviaEmail()
    {
        // Arrange
        var pedido = new Pedido
        {
            Cliente = new Cliente { Email = "test@test.com" }
        };
        
        _mockRepo
            .Setup(r => r.AgregarAsync(It.IsAny<Pedido>()))
            .Returns(Task.CompletedTask);
        
        _mockEmail
            .Setup(e => e.EnviarAsync(
                It.IsAny<string>(),
                It.IsAny<string>()))
            .Returns(Task.CompletedTask);
        
        // Act
        await _service.CrearPedidoAsync(pedido);
        
        // Assert
        _mockRepo.Verify(
            r => r.AgregarAsync(pedido),
            Times.Once);
        
        _mockEmail.Verify(
            e => e.EnviarAsync(
                "test@test.com",
                It.IsAny<string>()),
            Times.Once);
    }
}
```

---

## 📚 Recursos Recomendados

### Documentación Oficial
- [Microsoft C# Documentation](https://learn.microsoft.com/es-es/dotnet/csharp/)
- [.NET API Browser](https://learn.microsoft.com/es-es/dotnet/api/)

### Libros
- "Clean Code" - Robert C. Martin
- "C# in Depth" - Jon Skeet
- "Dependency Injection in .NET" - Mark Seemann

### Herramientas
- Visual Studio 2022 / VS Code
- ReSharper / Rider
- SonarLint (análisis de código)

### Sitios Web
- [C# Corner](https://www.c-sharpcorner.com/)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/c%23)

---

## 🎯 Checklist de Clean Code

Antes de hacer commit, verifica:

- [ ] Nombres descriptivos y en el idioma correcto
- [ ] Métodos pequeños (<20 líneas idealmente)
- [ ] Una responsabilidad por clase/método
- [ ] Validación de parámetros
- [ ] Manejo apropiado de null
- [ ] try-catch solo donde es necesario
- [ ] Comentarios solo cuando el código no es auto-explicativo
- [ ] Sin código muerto (comentado)
- [ ] Constantes en lugar de magic numbers
- [ ] Uso de async/await correctamente
- [ ] Tests unitarios para lógica de negocio

---

**Versión**: 2026.1  
**Actualizado**: Febrero 2026  
**C# Version**: 12/13
