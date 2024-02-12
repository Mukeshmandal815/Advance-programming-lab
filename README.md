# Advance-programming-lab
code formating
#include <stdio.h>
#include <stdbool.h>

// Define a structure for a graph node
struct Node {
    int nodeid;         // Node identifier
    int adjcount;       // Number of adjacent nodes
    int adjs[10];       // Array to store adjacent node IDs
    int costs[10];      // Array to store costs associated with adjacent nodes
};

// Function to add a node to the graph
int addNode(struct Node *p, int nid, int count) {
    int i = 0, ncount = count;
    // Check if the node already exists
    for (i = 0; i < count; i++) {
        if (p[i].nodeid == nid) {
            break;
        }
    }
    // If the node doesn't exist, add it to the graph
    if (i == count) {
        p[i].nodeid = nid;
        p[i].adjcount = 0;
        ncount++;
    }
    return ncount;
}

// Function to add an adjacent node to a given node
void addAdjacent(struct Node *p, int nid1, int nid2, int cost, int count) {
    int i = 0, index;
    // Find the index of the node in the graph
    for (i = 0; i < count; i++) {
        if (p[i].nodeid == nid1) {
            int c = p[i].adjcount;
            // Add the adjacent node and its cost
            p[i].adjs[c] = nid2;
            p[i].costs[c] = cost;
            p[i].adjcount = c + 1;

            break;
        }
    }
}

// Function to check if a node is already added to a list
int added(int *list, int lcount, int nid) {
    int i = 0;
    for (i = 0; i < lcount; i++) {
        if (list[i] == nid) {
            return 1;
        }
    }
    return 0;
}

// Function to find a path in the graph
void findpath(struct Node *p, int count, int start, int end, int *list, int *clist, int lcount, bool *essential) {
    int index = 0, i = 0;

    // Check if the list contains the end node
    if (list[lcount - 1] == end) {
        int tcost = 0;
        printf("\nPath:");
        // Print the path and calculate the total cost
        for (i = 0; i < lcount; i++) {
            printf(" %d ", list[i]);
            tcost += clist[i];
        }
        printf(" Cost = %d", tcost);

        printf("\nEssential Nodes:");
        // Print essential nodes
        for (i = 0; i < count; i++) {
            if (essential[i]) {
                printf(" %d ", p[i].nodeid);
            }
        }
        printf("\n");
        return;
    }

    // Find the index of the starting node
    for (i = 0; i < count; i++) {
        if (p[i].nodeid == start) {
            index = i;
            break;
        }
    }

    // Explore adjacent nodes
    for (i = 0; i < p[index].adjcount; i++) {
        int a = added(list, lcount, p[index].adjs[i]);
        if (a == 0) {
            list[lcount] = p[index].adjs[i];
            clist[lcount] = p[index].costs[i];
            lcount++;

            if (p[index].adjs[i] != end) {
                essential[index] = true;
            }

            findpath(p, count, p[index].adjs[i], end, list, clist, lcount, essential);
            lcount--;
            essential[index] = false;
        }
    }
}

// Main function
int main() {
    struct Node nodes[50];             // Array to store graph nodes
    bool essential[50] = {false};      // Array to track essential nodes
    int nodecount = 0;                  // Number of nodes in the graph
    int n1 = 0, n2 = 0, c = 0;          // Variables for node IDs and costs

    // Input loop to construct the graph
    while (1) {
        printf("n1, n2, cost ? ");
        scanf("%d %d %d", &n1, &n2, &c);
        if (n1 == -9 || n2 == -9 || c == -9) {
            break;
        }
        // Add nodes and their adjacent nodes
        nodecount = addNode(&nodes[0], n1, nodecount);
        nodecount = addNode(&nodes[0], n2, nodecount);

        addAdjacent(&nodes[0], n1, n2, c, nodecount);
        addAdjacent(&nodes[0], n2, n1, c, nodecount);
    }

    int start, end;
    printf("start, end ? ");
    scanf("%d %d", &start, &end);
    int list[50], clist[50], lcount = 0;
    list[0] = start;
    clist[0] = 0;
    lcount = 1;

    // Find and print the paths in the graph
    findpath(nodes, nodecount, start, end, list, clist, lcount, essential);

    return 0;
}

