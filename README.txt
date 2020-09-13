Database Schema for Movie Ticket Booking System
===============================================

/*
** Each theater in the multiplex will have an entry in the theater table. This gives
** information about the physical theater itself, but not about what movies are showing
** or the seats and reservations, which are all covered in other tables. The capacity
** is a fixed number of seats that are available. There is another table that will
** store the seats per row for each theater for ticketing.
*/

CREATE TABLE theater (
  id BIGINT UNIQUE NOT NULL GENERATED ALWAYS AS IDENTITY,
  theaterNumber INTEGER NOT NULL,
  capacity INTEGER NOT NULL
);

/*
** The screenings table stores information about the various movie screenings, including what
** theater it takes place in, the date of the screening (to accomodate different screening times
** on different days, the times that the screening starts, and foreign keys to the movie that
** theater it is showing in.
*/

CREATE TABLE screening (
  id BIGINT UNIQUE NOT NULL GENERATED ALWAYS AS IDENTITY,
  movieId BIGINT REFERENCES movie (id),
  theaterId BIGINT REFERENCES theater (id),
  date DATE NOT NULL,
  startTime TIME NOT NULL
);

/*
** The reservation table associates some user information and ticketing information with a
** specific seat in a specific screening.
*/

CREATE TABLE reservation (
  id BIGINT UNIQUE NOT NULL GENERATED ALWAYS AS IDENTITY,
  screeningId BIGINT REFERENCES screening (id),
  seatId BIGINT REFERENCES seat (id),
  ticketedName TEXT NOT NULL,
  ticketNumber CHAR(16) NOT NULL
);

/*
** Information about a specific seat in a specific theater. Like the theater table, the seat
** table describes a physical seat in a theater. It is a little more dynamic than the
** theater table, though, because we will keep information about whether the seat is out of
** order and needs repair. Out of order seats would not be presented for ticketing.
**
** The row value starts at one (the front of the theater) and increments toward the back.
** The seatInRow starts at one (the left-most seat when facing the screen) and increments
** as you walk right (facing the screen). The seatNumber is a string that matches the label
** on the seat ('A4', 'G12', etc.)
*/

CREATE TABLE seat (
  id BIGINT UNIQUE NOT NULL GENERATED ALWAYS AS IDENTITY,
  theaterId BIGINT REFERENCES theater (id),
  row INTEGER NOT NULL,
  seatInRow INTEGER NOT NULL,
  seatNumber CHAR(4) NOT NULL,
  outOfOrder BOOLEAN DEFAULT false
);

/*
** The movie table stores information about the movie. This information allows the users to
** find movies with a given title, description, and to filter by MPAA ratings.
*/

CREATE TABLE movie (
  id BIGINT UNIQUE NOT NULL GENERATED ALWAYS AS IDENTITY,
  name TEXT NOT NULL,
  description TEXT NOT NULL,
  mpaaRating mpaaRatingType NOT NULL,
  runLength INTEGER NOT NULL
);

CREATE TYPE mpaa_rating_type AS ENUM('G', 'PG', 'PG-13', 'R', 'NC-17');
