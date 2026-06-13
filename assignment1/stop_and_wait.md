## Problem Statement: 
the objective of this task is to design and simulate the stop and wait protocol. the problem involves establishing a reliable data transfer system requiring the sender to transmit a single frame and stop execution until a positive acknowledgment is received from the receiver.

## Solution Approach: 
the sender transmits a single character and pauses. if the random check passes, the ack is received, and the sender moves to the next character. if the random check fails , the sender retransmits the same character.


## code
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>

#define LOSS_PROBABILITY 0.3

// returns a random value between 0.0 - 1.0. 
double get_random_chance() {
    return (double)rand() / (double)RAND_MAX;
}

// Simulate Stop-and-Wait arq by resending a frame until it is acknowledged.
void stop_and_wait(const char* data) {
    int length = strlen(data);
    int i = 0;

    while (i < length) {
        // send the current frame one character at a time. 
        printf("[sender] transmitting frame %d: '%c'\n", i, data[i]);

        // randomly create a lost frame or ack.
        if (get_random_chance() < LOSS_PROBABILITY) {
            printf("[network] frame %d or its ack was lost.\n", i);
            printf("[sender] resending frame %d...\n\n", i);
            continue; 
        }

        // if the frame is not lost, the receiver acknowledges it and the sender moves on to nexxt one.
        printf("[receiver] ack sent for frame %d.\n", i);
        printf("[sender] ack received for frame %d.\n\n", i);
        i++; 
    }
    printf("stop and wait transmission complete.\n");
}

int main() {
    // seeds the random number generator to get different results each time the program runs.
    srand((unsigned int)time(NULL));
    const char* payload = "hello"; 

    printf("transmitting data\n");
    stop_and_wait(payload);

    return 0;
}
```