# Week 2

Welcome to my first dev blog about my Captstone project! This is a project I'm doing on a team (Team Squirtle)
with 3 classmates from my college program. The end goal is to release a game to an app store.

The first 2 weeks were about getting an initial idea and fleshing it out formally with various tools, such as 
Jira for project management, Confluence for documentation, GitHub for source control, and Discord for communication.
We're planning to get Jenkins set up next week.

As the start is mainly planning, most of the group worked on the same things. This included planning, setup, and writing up our GDD
and TDD. I've taken on a leadership role as I enjoy teaching, so I also spent a bit of time explaining concepts. 

![Screenshot of Team Squirtle's Jira Backlog](https://i.imgur.com/btQ6CKW.png)
*Our sprint board at the time of writing*

## GDD

In our Game Design Document, we followed a template. The template goes in depth regarding all design aspects of the game. We
filled it out as much as possible, maintaining good discussion as we did it. While writing up this document, we fleshed out
the majority of the team's ideas and got a bare minimum product in mind.

## TDD

The Technical Design Document was similar to the GDD, however I took a more active role in writing it. As a group, we discussed
what parts of the process we all enjoy the most, and I was designated the systems developer. Due to this, much of the higher level
technical design will fall on me to get systems in place for others to implement and use. I spent my time designing the purpose of our main
systems:

- UI / Flow: The way the player navigates the game
- Gameplay: The structures and object base classes that hold shared behaviour between gameplay objects
- Level loading: The system to allow for ease of designing levels, as well as the pipeline from asset to game
- Serialization: The logic to read/write player data from/to a file

I managed to get a basic serialization system going. The framework is there, but no data is currently being serialized.

## Teaching

As a Unity developer, I often use Delegates and event-based programming in my games. As these concepts were only briefly touched on to date
in our program, I wanted to give a mini lecture to my team about these topics so they wouldn't get overwhelmed when I use them in the systems.
In this mini-lecture, I explained delegates, how they're used, how they work internally, how event-based programming works conceptually and in practice, 
as well as the `event` access modifier.

## Summary
We didn't do too much programming this week, but we laid the foundation to start the production in the coming weeks. We have nearly all of the production
software set up, we just need to touch up our documentation.

---

*All the code in the codebase so far - the serialization system*
```cs
[Serializable]
public class Data
{
    public static Data Instance { get; private set; }

    static Data()
    {
        Deserialize();
    }
    
    #region Serialization
    private const string Filename = "player.dat";
    private static readonly string Path = @$"{Application.persistentDataPath}\{Filename}";
    
    public static void Deserialize()
    {
        try
        {
            using var file = File.OpenRead(Path);
            var formatter = new BinaryFormatter();
            Instance = (Data) formatter.Deserialize(file);
        }
        catch (FileNotFoundException) // No data found - normal
        {
            Debug.Log("Player data not found, creating new.");
            Instance = new Data();
        }
        catch (Exception e) // Unknown error
        {
            Debug.LogError($"Exception caught while deserializing player data: {e}");
            Application.Quit(-1);
        }
    }

    public static void Serialize()
    {
        using var file = File.OpenWrite(Path);
        var formatter = new BinaryFormatter();
        formatter.Serialize(file, Instance);
    }
    #endregion
}
```
