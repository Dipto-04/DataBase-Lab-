#include <iostream>
using namespace std;

int main() {
    int nBlocks, nProcesses;

    cout << "Enter number of memory blocks: ";
    cin >> nBlocks;

    int blocks[nBlocks];
    cout << "Enter sizes of memory blocks:\n";
    for(int i = 0; i < nBlocks; i++) {
        cin >> blocks[i];
    }

    cout << "Enter number of processes: ";
    cin >> nProcesses;

    int processes[nProcesses];
    cout << "Enter sizes of processes:\n";
    for(int i = 0; i < nProcesses; i++) {
        cin >> processes[i];
    }

    int allocation[nProcesses];
    for(int i = 0; i < nProcesses; i++) {
        allocation[i] = -1;   // not allocated initially
    }

    // Worst Fit Allocation
    for(int i = 0; i < nProcesses; i++) {
        int worstIndex = -1;

        for(int j = 0; j < nBlocks; j++) {
            if(blocks[j] >= processes[i]) {
                if(worstIndex == -1 || blocks[j] > blocks[worstIndex]) {
                    worstIndex = j;
                }
            }
        }

        if(worstIndex != -1) {
            allocation[i] = worstIndex;
            blocks[worstIndex] -= processes[i];  // reduce block size
        }
    }

    // Output
    cout << "\nProcess No.\tProcess Size\tBlock No.\n";
    for(int i = 0; i < nProcesses; i++) {
        cout << i + 1 << "\t\t" << processes[i] << "\t\t";
        if(allocation[i] != -1)
            cout << allocation[i] + 1;
        else
            cout << "Not Allocated";
        cout << endl;
    }

    return 0;
}
