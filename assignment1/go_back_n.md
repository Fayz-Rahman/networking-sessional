## Problem Statement: 
the objective of this task is to design and simulate the go-back-n protocol. The problem involves establishing a reliable data transfer system that allows the sender to transmit multiple frames up to a predefined window size without waiting for individual acknowledgments.

## Solution Approach: 
the sender transmits multiple characters continuously up to a maximum window size .If an ACK is received for the oldest unacknowledged packet, the window slides forward.If a timeout occurs (random check fails for the base packet), the sender discards any progress within that window and goes back to retransmit every character in the current window.


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

void go_back_n(const char* data, int window_size) {
    int base_index = 0;   
    int next_seq_num = 0; 
    int total_frames = strlen(data);

    while (base_index < total_frames) {
        // send frames until the current window is full. 
        while (next_seq_num < base_index + window_size && next_seq_num < total_frames) {
            printf("[sender] transmitting frame %d: '%c'\n", next_seq_num, data[next_seq_num]);
            next_seq_num++;
        }

        // randomly create a lost ack for the oldest frame.
        if (get_random_chance() < LOSS_PROBABILITY) {
            printf("[network] timeout. ack for frame %d was lost.\n", base_index);
            printf("[sender] going back to frame %d and resending window...\n\n", base_index);
            next_seq_num = base_index;
        } else {
            // ack received and slide the sender window by one frame.
            printf("[receiver] ack sent for frame %d.\n", base_index);
            printf("[sender] ack received for frame %d. window slides forward.\n\n", base_index);
            base_index++;
        }
    }
    printf("go back n transmission complete.\n");
}


int main() {
    // seeds the random number generator to get different results each time the program runs.   
    srand((unsigned int)time(NULL));
    const char* payload = "hello"; 
    int window_size = 3;

    printf("transmitting data\n");
    go_back_n(payload, window_size);

    return 0;
}
```