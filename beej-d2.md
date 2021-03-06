-- Turn on foreign key constraints (SQLite only)

PRAGMA foreign_keys = ON;

-- Creating the tables:

CREATE TABLE album (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    title VARCHAR(128) NOT NULL,
    release_year INTEGER
);

CREATE TABLE artist (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name VARCHAR(128) NOT NULL
);

CREATE TABLE track (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    title VARCHAR(128) NOT NULL,
    album_id   INTEGER, --foreign key
    FOREIGN KEY (album_id) REFERENCES album (id));


CREATE TABLE artist_album (
    artist_id INTEGER,
    album_id INTEGER,
    FOREIGN KEY(artist_id) REFERENCES artist(id),
    FOREIGN KEY(album_id) REFERENCES album(id)
);

-- Populate tables

INSERT INTO album (title, release_year) VALUES ("Super Awesome Album", 1990);
INSERT INTO album (title) VALUES ("Super Funky Album");
INSERT INTO album (title, release_year) VALUES ("Super Disco Album", 1978);
INSERT INTO album (title, release_year) VALUES ("Super Hairband Album", 1984);
INSERT INTO album (title) VALUES ("Super Dubstep Album");

INSERT INTO artist (name) VALUES ("Luke and the Droidtones");
INSERT INTO artist (name) VALUES ("Leia and the Ewoks");
INSERT INTO artist (name) VALUES ("Han Solo");

INSERT INTO artist_album (artist_id, album_id) VALUES (1, 5);
INSERT INTO artist_album (artist_id, album_id) VALUES (1, 2);
INSERT INTO artist_album (artist_id, album_id) VALUES (2, 1);
INSERT INTO artist_album (artist_id, album_id) VALUES (2, 2);
INSERT INTO artist_album (artist_id, album_id) VALUES (3, 3);
INSERT INTO artist_album (artist_id, album_id) VALUES (3, 4);

INSERT INTO track (title, album_id) VALUES ("Super Awesome Track 1", 1);
INSERT INTO track (title, album_id) VALUES ("Super Awesome Track 2", 1);
INSERT INTO track (title, album_id) VALUES ("Super Awesome Track 3", 1);
INSERT INTO track (title, album_id) VALUES ("Super Awesome Track 4", 1);
INSERT INTO track (title, album_id) VALUES ("Super Awesome Track 5", 1);

INSERT INTO track (title, album_id) VALUES ("Super Funky Track 1", 2);
INSERT INTO track (title, album_id) VALUES ("Super Funky Track 2", 2);
INSERT INTO track (title, album_id) VALUES ("Super Funky Track 3", 2);
INSERT INTO track (title, album_id) VALUES ("Super Funky Track 4", 2);

INSERT INTO track (title, album_id) VALUES ("Super Disco Track 1", 3);
INSERT INTO track (title, album_id) VALUES ("Super Disco Track 2", 3);
INSERT INTO track (title, album_id) VALUES ("Super Disco Track 3", 3);

INSERT INTO track (title, album_id) VALUES ("Super Hairband Track 1", 4);
INSERT INTO track (title, album_id) VALUES ("Super Hairband Track 2", 4);
INSERT INTO track (title, album_id) VALUES ("Super Hairband Track 3", 4);
INSERT INTO track (title, album_id) VALUES ("Super Hairband Track 4", 4);
INSERT INTO track (title, album_id) VALUES ("Super Hairband Track 5", 4);
INSERT INTO track (title, album_id) VALUES ("Super Hairband Track 6", 4);
INSERT INTO track (title, album_id) VALUES ("Super Hairband Track 7", 4);

INSERT INTO track (title, album_id) VALUES ("Super Dubstep Track 1", 5);
INSERT INTO track (title, album_id) VALUES ("Super Dubstep Track 2", 5);
INSERT INTO track (title, album_id) VALUES ("Super Dubstep Track 3", 5);
INSERT INTO track (title, album_id) VALUES ("Super Dubstep Track 4", 5);
INSERT INTO track (title, album_id) VALUES ("Super Dubstep Track 5", 5);


-- Show all tracks

SELECT * FROM track;

-- All albums between 1975 and 1990

SELECT * FROM album WHERE release_year <= 1990 AND release_year >= 1975;
SELECT * FROM album WHERE release_year BETWEEN 1975 AND 1990;

-- All the start with "Super D"

SELECT * FROM album WHERE title like 'Super D%';

-- Same, with "any letter" instead of "p" in "Super

SELECT * FROM album WHERE title like 'Su_er D%';

-- Anything that ends with "Album"

SELECT * FROM album WHERE title like '%Album';

-- Anything with a "k" anywhere in the title

SELECT * FROM album WHERE title like '%k%';

-- All albums with no release year (NULL release_year)

SELECT * FROM album WHERE release_year IS NULL;

-- All tracks from Super Funky Album

SELECT track.title
    FROM track, album
    WHERE track.album_id = album.id
    AND album.title = "Super Funky Album";

-- All tracks from all albums

SELECT track.title FROM track, album WHERE track.album_id = album.id;
             
-- Same, but also show album title

SELECT track.title, album.title FROM track, album WHERE track.album_id = album.id;

-- All album titles from artist Han Solo

SELECT album.title
    FROM album, artist_album, artist
    WHERE artist_album.album_id = album.id
    AND artist_album.artist_id = artist.id
    AND artist.name = "Han Solo";
         
-- Same, with subselects

SELECT title FROM album WHERE id IN
    (SELECT album_id FROM artist_album WHERE artist_id =
        (SELECT id FROM artist WHERE name = "Han Solo"));
         
-- Average of albums' release years

SELECT AVG(release_year) FROM album;

-- Same, since AVG ignores NULL rows
         
SELECT AVG(release_year) FROM album WHERE release_year is not null;
ase_year)

-- Average release year for albums by Leia and the Ewoks

SELECT AVG(release_year) FROM album, artist_album, artist
    WHERE artist_album.artist_id = artist.id
    AND artist_album.album_id = album.id
    AND artist.name = "Leia and the Ewoks";
         
-- Count of rows from artist with non-NULL id fields
SELECT COUNT(id) FROM artist;

-- Count of rows from artist, unconditionally
SELECT COUNT(*) FROM artist;
  
-- Count of rows from artist, renamed to "total"
  
SELECT COUNT(*) AS total FROM artist;
  
-- Count of tracks on Super Dubstep Album

SELECT COUNT(*) FROM track, album WHERE track.album_id = album.id AND album.title = 'Super Dubstep Album';
  

-- Count of album release years, not counting NULL entries

SELECT count(release_year) FROM album;
           
-- Example of NOT a SQL injection attack, when the user enters "beej"

-- SELECT * FROM foo WHERE user = 'beej';

-- Example of a SQL injection attack, when the user enters
-- "beej'; DROP TABLE foo; --"

-- SELECT * FROM foo WHERE user = 'beej'; DROP TABLE foo; --';