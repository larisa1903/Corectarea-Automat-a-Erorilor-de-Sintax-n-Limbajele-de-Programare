# Corectarea-Automat-a-Erorilor-de-Sintax-n-Limbajele-de-Programare
# Structura Directorului
|-- README.md
|-- src
|   |-- main.c
|   |-- levenshtein.c
|   |-- levenshtein.h
|-- test
|   |-- generate_test_data.c
|   |-- test_cases.txt
|-- Makefile
# Fișierul README.md
# Corectarea Automată a Erorilor de Sintaxă

## Descriere
Acest proiect implementează un algoritm pentru corectarea automată a erorilor de sintaxă în fragmente de cod, utilizând distanța Levenshtein pentru a calcula numărul minim de operații necesare pentru a transforma un fragment de cod într-unul valid.

## Structura Proiectului
- `src/`: conține fișierele sursă ale aplicației.
  - `main.c`: funcția principală.
  - `levenshtein.c`: implementarea algoritmului de distanță Levenshtein.
  - `levenshtein.h`: antetul pentru `levenshtein.c`.
- `test/`: conține fișiere pentru generarea datelor de test și testarea algoritmului.
  - `generate_test_data.c`: generarea automată a datelor de test.
  - `test_cases.txt`: seturi de date de intrare non-triviale.
- `Makefile`: fișier pentru compilarea proiectului.

make
./syntax_correction
./generate_test_data

#### Makefile

```makefile
CC = gcc
CFLAGS = -Wall -Wextra -pedantic
SRC = src/main.c src/levenshtein.c
OBJ = $(SRC:.c=.o)
EXEC = syntax_correction
TEST_SRC = test/generate_test_data.c
TEST_EXEC = generate_test_data

all: $(EXEC) $(TEST_EXEC)

$(EXEC): $(OBJ)
	$(CC) $(CFLAGS) -o $@ $^

$(TEST_EXEC): $(TEST_SRC)
	$(CC) $(CFLAGS) -o $@ $^

clean:
	rm -f $(OBJ) $(EXEC) $(TEST_EXEC)


# src/main.c



#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include "levenshtein.h"

/**
 * Funcția principală care calculează numărul minim de operații necesare pentru
 * a corecta fragmentul de cod dat conform regulii specificate.
 */
int main() {
    char fragment[] = "fnuc(myFuncion";
    char valid[] = "func(myFunction)";

    int dist = levenshtein_distance(fragment, valid);
    printf("Numărul minim de operații necesare: %d\n", dist);

    return 0;
}


# src/levenshtein.c



#include "levenshtein.h"

/**
 * Returnează minimul dintre trei numere întregi.
 */
int min(int a, int b, int c) {
    if (a <= b && a <= c) return a;
    if (b <= a && b <= c) return b;
    return c;
}

/**
 * Calculează distanța Levenshtein dintre două șiruri de caractere.
 */
int levenshtein_distance(const char *s, const char *t) {
    int n = strlen(s);
    int m = strlen(t);

    int d[n + 1][m + 1];

    for (int i = 0; i <= n; i++) d[i][0] = i;
    for (int j = 0; j <= m; j++) d[0][j] = j;

    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            int cost = (s[i - 1] == t[j - 1]) ? 0 : 1;
            d[i][j] = min(d[i - 1][j] + 1, d[i][j - 1] + 1, d[i - 1][j - 1] + cost);
        }
    }

    return d[n][m];
}


#src/levenshtein.h


#ifndef LEVENSHTEIN_H
#define LEVENSHTEIN_H

/**
 * Calculează distanța Levenshtein dintre două șiruri de caractere.
 * 
 * @param s Primul șir de caractere.
 * @param t Al doilea șir de caractere.
 * @return Numărul minim de operații necesare pentru a transforma s în t.
 */
int levenshtein_distance(const char *s, const char *t);

#endif


#test/generate_test_data.c

#include <stdio.h>
#include <stdlib.h>
#include <time.h>

/**
 * Generează un caracter aleatoriu.
 * 
 * @return Un caracter aleatoriu din alfabetul englez.
 */
char gen_random_char() {
    return "abcdefghijklmnopqrstuvwxyz"[rand() % 26];
}

/**
 * Generează date de test și le scrie într-un fișier.
 * 
 * @param length Lungimea datelor de test generate.
 * @param filename Numele fișierului în care se vor scrie datele.
 */
void generate_test_data(int length, const char *filename) {
    FILE *file = fopen(filename, "w");
    if (!file) {
        perror("Could not open file");
        exit(EXIT_FAILURE);
    }

    for (int i = 0; i < length; i++) {
        fprintf(file, "%c", gen_random_char());
    }
    fprintf(file, "\n");

    fclose(file);
}

/**
 * Funcția principală pentru generarea datelor de test.
 */
int main() {
    srand(time(0));
    generate_test_data(1000, "test/test_cases.txt");
    printf("Data de test generate în test/test_cases.txt\n");

    return 0;
}


