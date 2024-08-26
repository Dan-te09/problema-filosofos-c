#include <iostream>
#include <thread>
#include <mutex>
#include <chrono>

const int NUM_FILOSOFOS = 5;

// Array de mutexes para los tenedores
std::mutex tenedores[NUM_FILOSOFOS];

// Función que simula la acción de comer para un filósofo
void filosofar(int id) {
    int izquierda = id;
    int derecha = (id + 1) % NUM_FILOSOFOS;

    while (true) {
        // Pensar
        std::cout << "Filósofo " << id << " está pensando." << std::endl;
        std::this_thread::sleep_for(std::chrono::seconds(1));

        // Adquirir tenedores en orden
        if (izquierda < derecha) {
            tenedores[izquierda].lock();
            tenedores[derecha].lock();
        } else {
            tenedores[derecha].lock();
            tenedores[izquierda].lock();
        }

        // Comer
        std::cout << "Filósofo " << id << " está comiendo." << std::endl;
        std::this_thread::sleep_for(std::chrono::seconds(1));

        // Liberar tenedores
        tenedores[derecha].unlock();
        tenedores[izquierda].unlock();
    }
}

int main() {
    std::thread filosofos[NUM_FILOSOFOS];

    // Crear hilos para los filósofos
    for (int i = 0; i < NUM_FILOSOFOS; ++i) {
        filosofos[i] = std::thread(filosofar, i);
    }

    // Unir los hilos (esto en realidad no terminará nunca en este caso)
    for (int i = 0; i < NUM_FILOSOFOS; ++i) {
        filosofos[i].join();
    }

    return 0;
}
