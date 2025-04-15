// matrix.c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include "matrix.h"

int **crearMatriz(int n) {
    int **matriz = (int **)malloc(n * sizeof(int *));
    for (int i = 0; i < n; i++) {
        matriz[i] = (int *)malloc(n * sizeof(int));
    }
    return matriz;
}

void liberarMatriz(int **matriz, int n) {
    for (int i = 0; i < n; i++) {
        free(matriz[i]);
    }
    free(matriz);
}

void inicializarMatriz(int **matriz, int n) {
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            matriz[i][j] = rand() % 10;
        }
    }
}

void imprimirMatriz(int **matriz, int n) {
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            printf("%d ", matriz[i][j]);
        }
        printf("\n");
    }
}

void multiplicacionTradicional(int **A, int **B, int **C, int n) {
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            C[i][j] = 0;
            for (int k = 0; k < n; k++) {
                C[i][j] += A[i][k] * B[k][j];
            }
        }
    }
}

void sumarMatrices(int **A, int **B, int **C, int n) {
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            C[i][j] = A[i][j] + B[i][j];
        }
    }
}

void restarMatrices(int **A, int **B, int **C, int n) {
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            C[i][j] = A[i][j] - B[i][j];
        }
    }
}

void copiarSubmatriz(int **origen, int **destino, int fila, int columna, int n) {
    for (int i = 0; i < n; i++) {
        memcpy(destino[i], &origen[i + fila][columna], n * sizeof(int));
    }
}

void pegarSubmatriz(int **origen, int **destino, int fila, int columna, int n) {
    for (int i = 0; i < n; i++) {
        memcpy(&destino[i + fila][columna], origen[i], n * sizeof(int));
    }
}

void multiplicacionDAC(int **A, int **B, int **C, int n) {
    if (n == 1) {
        C[0][0] = A[0][0] * B[0][0];
        return;
    }
    int mitad = n / 2;
    int **A11 = crearMatriz(mitad); int **A12 = crearMatriz(mitad);
    int **A21 = crearMatriz(mitad); int **A22 = crearMatriz(mitad);
    int **B11 = crearMatriz(mitad); int **B12 = crearMatriz(mitad);
    int **B21 = crearMatriz(mitad); int **B22 = crearMatriz(mitad);
    int **C11 = crearMatriz(mitad); int **C12 = crearMatriz(mitad);
    int **C21 = crearMatriz(mitad); int **C22 = crearMatriz(mitad);
    int **T1 = crearMatriz(mitad); int **T2 = crearMatriz(mitad);
    copiarSubmatriz(A, A11, 0, 0, mitad);
    copiarSubmatriz(A, A12, 0, mitad, mitad);
    copiarSubmatriz(A, A21, mitad, 0, mitad);
    copiarSubmatriz(A, A22, mitad, mitad, mitad);
    copiarSubmatriz(B, B11, 0, 0, mitad);
    copiarSubmatriz(B, B12, 0, mitad, mitad);
    copiarSubmatriz(B, B21, mitad, 0, mitad);
    copiarSubmatriz(B, B22, mitad, mitad, mitad);
    // C11 = A11*B11 + A12*B21
    multiplicacionDAC(A11, B11, T1, mitad);
    multiplicacionDAC(A12, B21, T2, mitad);
    sumarMatrices(T1, T2, C11, mitad);
    // C12 = A11*B12 + A12*B22
    multiplicacionDAC(A11, B12, T1, mitad);
    multiplicacionDAC(A12, B22, T2, mitad);
    sumarMatrices(T1, T2, C12, mitad);
    // C21 = A21*B11 + A22*B21
    multiplicacionDAC(A21, B11, T1, mitad);
    multiplicacionDAC(A22, B21, T2, mitad);
    sumarMatrices(T1, T2, C21, mitad);
    // C22 = A21*B12 + A22*B22
    multiplicacionDAC(A21, B12, T1, mitad);
    multiplicacionDAC(A22, B22, T2, mitad);
    sumarMatrices(T1, T2, C22, mitad);
    pegarSubmatriz(C11, C, 0, 0, mitad);
    pegarSubmatriz(C12, C, 0, mitad, mitad);
    pegarSubmatriz(C21, C, mitad, 0, mitad);
    pegarSubmatriz(C22, C, mitad, mitad, mitad);
    liberarMatriz(A11, mitad); liberarMatriz(A12, mitad);
    liberarMatriz(A21, mitad); liberarMatriz(A22, mitad);
    liberarMatriz(B11, mitad); liberarMatriz(B12, mitad);
    liberarMatriz(B21, mitad); liberarMatriz(B22, mitad);
    liberarMatriz(C11, mitad); liberarMatriz(C12, mitad);
    liberarMatriz(C21, mitad); liberarMatriz(C22, mitad);
    liberarMatriz(T1, mitad); liberarMatriz(T2, mitad);
}

void multiplicacionStrassen(int **A, int **B, int **C, int n) {
    if (n == 1) {
        C[0][0] = A[0][0] * B[0][0];
        return;
    }
    int mitad = n / 2;
    int **A11 = crearMatriz(mitad); int **A12 = crearMatriz(mitad);
    int **A21 = crearMatriz(mitad); int **A22 = crearMatriz(mitad);
    int **B11 = crearMatriz(mitad); int **B12 = crearMatriz(mitad);
    int **B21 = crearMatriz(mitad); int **B22 = crearMatriz(mitad);
    copiarSubmatriz(A, A11, 0, 0, mitad);
    copiarSubmatriz(A, A12, 0, mitad, mitad);
    copiarSubmatriz(A, A21, mitad, 0, mitad);
    copiarSubmatriz(A, A22, mitad, mitad, mitad);
    copiarSubmatriz(B, B11, 0, 0, mitad);
    copiarSubmatriz(B, B12, 0, mitad, mitad);
    copiarSubmatriz(B, B21, mitad, 0, mitad);
    copiarSubmatriz(B, B22, mitad, mitad, mitad);
    int **M1 = crearMatriz(mitad);
    int **M2 = crearMatriz(mitad);
    int **M3 = crearMatriz(mitad);
    int **M4 = crearMatriz(mitad);
    int **M5 = crearMatriz(mitad);
    int **M6 = crearMatriz(mitad);
    int **M7 = crearMatriz(mitad);
    int **T1 = crearMatriz(mitad);
    int **T2 = crearMatriz(mitad);
    sumarMatrices(A11, A22, T1, mitad);
    sumarMatrices(B11, B22, T2, mitad);
    multiplicacionStrassen(T1, T2, M1, mitad);
    sumarMatrices(A21, A22, T1, mitad);
    multiplicacionStrassen(T1, B11, M2, mitad);
    restarMatrices(B12, B22, T2, mitad);
    multiplicacionStrassen(A11, T2, M3, mitad);
    restarMatrices(B21, B11, T2, mitad);
    multiplicacionStrassen(A22, T2, M4, mitad);
    sumarMatrices(A11, A12, T1, mitad);
    multiplicacionStrassen(T1, B22, M5, mitad);
    restarMatrices(A21, A11, T1, mitad);
    sumarMatrices(B11, B12, T2, mitad);
    multiplicacionStrassen(T1, T2, M6, mitad);
    restarMatrices(A12, A22, T1, mitad);
    sumarMatrices(B21, B22, T2, mitad);
    multiplicacionStrassen(T1, T2, M7, mitad);
    int **C11 = crearMatriz(mitad);
    int **C12 = crearMatriz(mitad);
    int **C21 = crearMatriz(mitad);
    int **C22 = crearMatriz(mitad);
    sumarMatrices(M1, M4, T1, mitad);
    restarMatrices(T1, M5, T2, mitad);
    sumarMatrices(T2, M7, C11, mitad);
    sumarMatrices(M3, M5, C12, mitad);
    sumarMatrices(M2, M4, C21, mitad);
    sumarMatrices(M1, M3, T1, mitad);
    restarMatrices(T1, M2, T2, mitad);
    sumarMatrices(T2, M6, C22, mitad);
    pegarSubmatriz(C11, C, 0, 0, mitad);
    pegarSubmatriz(C12, C, 0, mitad, mitad);
    pegarSubmatriz(C21, C, mitad, 0, mitad);
    pegarSubmatriz(C22, C, mitad, mitad, mitad);
    liberarMatriz(A11, mitad); liberarMatriz(A12, mitad);
    liberarMatriz(A21, mitad); liberarMatriz(A22, mitad);
    liberarMatriz(B11, mitad); liberarMatriz(B12, mitad);
    liberarMatriz(B21, mitad); liberarMatriz(B22, mitad);
    liberarMatriz(M1, mitad); liberarMatriz(M2, mitad);
    liberarMatriz(M3, mitad); liberarMatriz(M4, mitad);
    liberarMatriz(M5, mitad); liberarMatriz(M6, mitad);
    liberarMatriz(M7, mitad); liberarMatriz(T1, mitad);
    liberarMatriz(T2, mitad); liberarMatriz(C11, mitad);
    liberarMatriz(C12, mitad); liberarMatriz(C21, mitad);
    liberarMatriz(C22, mitad);
}
