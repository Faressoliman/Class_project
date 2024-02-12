# Class_project
Sure, here's a Python program using SQLite to manage a music library:

```python
import sqlite3

# Function to create a new database or connect to an existing one
def create_connection(database):
    conn = None
    try:
        conn = sqlite3.connect(database)
        print("Connected to SQLite database")
    except sqlite3.Error as e:
        print(e)
    return conn

# Function to create a table for music library if it doesn't exist
def create_table(conn):
    try:
        cursor = conn.cursor()
        cursor.execute('''CREATE TABLE IF NOT EXISTS music (
                            id INTEGER PRIMARY KEY,
                            artist TEXT NOT NULL,
                            album TEXT NOT NULL,
                            track TEXT NOT NULL
                            )''')
        conn.commit()
        print("Table 'music' created successfully")
    except sqlite3.Error as e:
        print(e)

# Function to add a song to the music library
def add_song(conn, artist, album, track):
    try:
        cursor = conn.cursor()
        cursor.execute("INSERT INTO music (artist, album, track) VALUES (?, ?, ?)", (artist, album, track))
        conn.commit()
        print("Song added successfully")
    except sqlite3.Error as e:
        print(e)

# Function to remove a song from the music library
def remove_song(conn, track):
    try:
        cursor = conn.cursor()
        cursor.execute("DELETE FROM music WHERE track=?", (track,))
        conn.commit()
        print("Song removed successfully")
    except sqlite3.Error as e:
        print(e)

# Function to update a song in the music library
def update_song(conn, track, new_artist, new_album, new_track):
    try:
        cursor = conn.cursor()
        cursor.execute("UPDATE music SET artist=?, album=?, track=? WHERE track=?", (new_artist, new_album, new_track, track))
        conn.commit()
        print("Song updated successfully")
    except sqlite3.Error as e:
        print(e)

# Function to search for songs, artists, or albums in the music library
def search(conn, keyword):
    try:
        cursor = conn.cursor()
        cursor.execute("SELECT * FROM music WHERE artist LIKE ? OR album LIKE ? OR track LIKE ?", ('%'+keyword+'%', '%'+keyword+'%', '%'+keyword+'%'))
        rows = cursor.fetchall()
        if rows:
            print("Search results:")
            for row in rows:
                print(row)
        else:
            print("No results found")
    except sqlite3.Error as e:
        print(e)

def main():
    database = "music_library.db"
    conn = create_connection(database)
    if conn is not None:
        create_table(conn)
    else:
        print("Error! Cannot create database connection.")

    while True:
        print("\n********** Music Library **********")
        print("1. Add a song")
        print("2. Remove a song")
        print("3. Update a song")
        print("4. Search for songs, artists, or albums")
        print("5. Exit")
        choice = input("Enter your choice: ")

        if choice == '1':
            artist = input("Enter artist name: ")
            album = input("Enter album name: ")
            track = input("Enter track title: ")
            add_song(conn, artist, album, track)
        elif choice == '2':
            track = input("Enter track title to remove: ")
            remove_song(conn, track)
        elif choice == '3':
            track = input("Enter track title to update: ")
            new_artist = input("Enter new artist name: ")
            new_album = input("Enter new album name: ")
            new_track = input("Enter new track title: ")
            update_song(conn, track, new_artist, new_album, new_track)
        elif choice == '4':
            keyword = input("Enter keyword to search: ")
            search(conn, keyword)
        elif choice == '5':
            print("Exiting...")
            break
        else:
            print("Invalid choice. Please enter a valid option.")

    conn.close()

if __name__ == '__main__':
    main()
