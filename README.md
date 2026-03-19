using System;
using System.Collections.Generic;
using System.IO;
using System.Text.Json;

class Program
{
    static void Main()
    {
        var usuarios = JsonSerializer.Deserialize<List<Usuario>>(File.ReadAllText("usuarios.json"));

        Console.Write("Email: ");
        string email = Console.ReadLine();

        Console.Write("Contraseña: ");
        string pass = Console.ReadLine();

        var user = usuarios.Find(u => u.Email == email && u.Contrasena == pass);

        if (user == null)
        {
            Console.WriteLine("Login incorrecto");
            return;
        }

        Console.WriteLine("Bienvenido!");

        var tareas = File.Exists("tareas.json")
            ? JsonSerializer.Deserialize<Dictionary<string, List<string>>>(File.ReadAllText("tareas.json"))
            : new Dictionary<string, List<string>>();

        if (!tareas.ContainsKey(email))
            tareas[email] = new List<string>();

        int op;
        do
        {
            Console.WriteLine("\n1.Ver tareas  2.Agregar  0.Salir");
            op = int.Parse(Console.ReadLine());

            if (op == 1)
                tareas[email].ForEach(t => Console.WriteLine("- " + t));

            if (op == 2)
            {
                Console.Write("Nueva tarea: ");
                tareas[email].Add(Console.ReadLine());
                File.WriteAllText("tareas.json",
                    JsonSerializer.Serialize(tareas, new JsonSerializerOptions { WriteIndented = true }));
            }

        } while (op != 0);
    }
}

class Usuario
{
    public string Email { get; set; }
    public string Contrasena { get; set; }
}
