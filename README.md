# Aads-practicals
1.	Write a program to sort the elements of an array using Randomized Quick Sort (the program should report the number of comparisons).

CODE:
#include <iostream>
#include <vector>
#include <cstdlib>
#include <ctime>
using namespace std;

long long comparisons = 0;

int partition(vector<int>& a, int low, int high) {

    // Select random pivot
    int randomIndex = low + rand() % (high - low + 1);

    swap(a[randomIndex], a[high]);

    int pivot = a[high];

    int i = low - 1;

    for (int j = low; j < high; j++) {

        comparisons++;

        if (a[j] <= pivot) {
            i++;
            swap(a[i], a[j]);
        }
    }

    swap(a[i + 1], a[high]);

    return i + 1;
}

void randomizedQuickSort(vector<int>& a, int low, int high) {

    if (low < high) {

        int q = partition(a, low, high);

        randomizedQuickSort(a, low, q - 1);

        randomizedQuickSort(a, q + 1, high);
    }
}

int main() {

    srand(time(0));

    int n;

    cout << "Enter number of elements: ";
    cin >> n;

    vector<int> a(n);

    cout << "Enter array elements: ";

    for (int i = 0; i < n; i++) {
        cin >> a[i];
    }

    randomizedQuickSort(a, 0, n - 1);

    cout << "\nSorted array: ";

    for (int x : a) {
        cout << x << " ";
    }

    cout << "\nNumber of comparisons: " << comparisons;

    return 0;
}

2.	Write a program to find the ith smallest element of an array using Randomized Select.

CODE:
#include <iostream>
#include <vector>
#include <cstdlib>
#include <ctime>
using namespace std;

int partition(vector<int>& a, int low, int high) {

    int randomIndex = low + rand() % (high - low + 1);

    swap(a[randomIndex], a[high]);

    int pivot = a[high];

    int i = low - 1;

    for (int j = low; j < high; j++) {

        if (a[j] <= pivot) {
            i++;
            swap(a[i], a[j]);
        }
    }

    swap(a[i + 1], a[high]);

    return i + 1;
}

int randomizedSelect(vector<int>& a, int low, int high, int k) {

    if (low == high) {
        return a[low];
    }

    int q = partition(a, low, high);

    // Rank of pivot in current subarray
    int rank = q - low + 1;

    if (k == rank) {
        return a[q];
    }

    else if (k < rank) {
        return randomizedSelect(a, low, q - 1, k);
    }

    else {
        return randomizedSelect(a, q + 1, high, k - rank);
    }
}

int main() {

    srand(time(0));

    int n, k;

    cout << "Enter number of elements: ";
    cin >> n;

    vector<int> a(n);

    cout << "Enter array elements: ";

    for (int i = 0; i < n; i++) {
        cin >> a[i];
    }

    cout << "Enter value of k: ";
    cin >> k;

    if (k < 1 || k > n) {
        cout << "Invalid value of k";
        return 0;
    }

    int result = randomizedSelect(a, 0, n - 1, k);

    cout << k << "th smallest element is: " << result;

    return 0;
}

3.	Write a program to determine the minimum spanning tree of a graph using Kruskal’s algorithm.

CODE:
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

struct Edge {
    int u, v, weight;
};

bool compare(Edge a, Edge b) {
    return a.weight < b.weight;
}

int parent[1000];
int rnk[1000];

// Find operation
int find(int x) {

    if (parent[x] != x) {
        parent[x] = find(parent[x]);
    }

    return parent[x];
}

// Union operation
void unite(int x, int y) {

    int rootX = find(x);
    int rootY = find(y);

    if (rootX == rootY)
        return;

    if (rnk[rootX] < rnk[rootY]) {
        parent[rootX] = rootY;
    }

    else if (rnk[rootX] > rnk[rootY]) {
        parent[rootY] = rootX;
    }

    else {
        parent[rootY] = rootX;
        rnk[rootX]++;
    }
}

int main() {

    int V, E;

    cout << "Enter number of vertices and edges: ";
    cin >> V >> E;

    vector<Edge> edges(E);

    cout << "Enter edges (u v weight):\n";

    for (int i = 0; i < E; i++) {
        cin >> edges[i].u >> edges[i].v >> edges[i].weight;
    }

    // Initialize DSU
    for (int i = 0; i < V; i++) {
        parent[i] = i;
        rnk[i] = 0;
    }

    // Sort edges by weight
    sort(edges.begin(), edges.end(), compare);

    int totalWeight = 0;
    int count = 0;

    cout << "\nEdges in Minimum Spanning Tree:\n";

    for (auto e : edges) {

        if (find(e.u) != find(e.v)) {

            cout << e.u << " - " << e.v
                 << " : " << e.weight << endl;

            totalWeight += e.weight;

            unite(e.u, e.v);

            count++;

            if (count == V - 1)
                break;
        }
    }

    if (count != V - 1) {
        cout << "MST does not exist because graph is disconnected.\n";
    }
    else {
        cout << "\nTotal MST Weight: " << totalWeight;
    }

    return 0;
}

4.	Write a program to implement the Bellman-Ford algorithm to find the shortest paths from a given source node to all other nodes in a graph.

CODE:

#include <iostream>
#include <vector>
using namespace std;

struct Edge {
    int u, v, weight;
};

int main() {

    int V, E;

    cout << "Enter number of vertices and edges: ";
    cin >> V >> E;

    vector<Edge> edges(E);

    cout << "Enter edges (source destination weight):\n";

    for (int i = 0; i < E; i++) {
        cin >> edges[i].u >> edges[i].v >> edges[i].weight;
    }

    int source;

    cout << "Enter source vertex: ";
    cin >> source;

    const int INF = 1000000000;

    vector<int> dist(V, INF);

    dist[source] = 0;

    // Relax all edges V-1 times
    for (int i = 1; i <= V - 1; i++) {

        bool updated = false;

        for (auto e : edges) {

            if (dist[e.u] != INF &&
                dist[e.u] + e.weight < dist[e.v]) {

                dist[e.v] = dist[e.u] + e.weight;

                updated = true;
            }
        }

        if (!updated)
            break;
    }

    // Check for negative weight cycle
    for (auto e : edges) {

        if (dist[e.u] != INF &&
            dist[e.u] + e.weight < dist[e.v]) {

            cout << "Graph contains a reachable negative weight cycle.";

            return 0;
        }
    }

    cout << "\nShortest distances from source " << source << ":\n";

    for (int i = 0; i < V; i++) {

        cout << "Vertex " << i << " : ";

        if (dist[i] == INF)
            cout << "INF";
        else
            cout << dist[i];

        cout << endl;
    }

    return 0;
}

5.	Write a program to implement a B-Tree.

CODE:
#include <iostream>
using namespace std;

class BTreeNode {

public:

    int *keys;

    BTreeNode **child;

    int t;

    int n;

    bool leaf;

    BTreeNode(int degree, bool isLeaf) {

        t = degree;

        leaf = isLeaf;

        keys = new int[2 * t - 1];

        child = new BTreeNode*[2 * t];

        n = 0;
    }

    void traverse() {

        int i;

        for (i = 0; i < n; i++) {

            if (!leaf)
                child[i]->traverse();

            cout << keys[i] << " ";
        }

        if (!leaf)
            child[i]->traverse();
    }

    bool search(int k) {

        int i = 0;

        while (i < n && k > keys[i])
            i++;

        if (i < n && keys[i] == k)
            return true;

        if (leaf)
            return false;

        return child[i]->search(k);
    }

    void splitChild(int i, BTreeNode *y) {

        BTreeNode *z = new BTreeNode(t, y->leaf);

        z->n = t - 1;

        for (int j = 0; j < t - 1; j++)
            z->keys[j] = y->keys[j + t];

        if (!y->leaf) {

            for (int j = 0; j < t; j++)
                z->child[j] = y->child[j + t];
        }

        y->n = t - 1;

        for (int j = n; j >= i + 1; j--)
            child[j + 1] = child[j];

        child[i + 1] = z;

        for (int j = n - 1; j >= i; j--)
            keys[j + 1] = keys[j];

        keys[i] = y->keys[t - 1];

        n++;
    }

    void insertNonFull(int k) {

        int i = n - 1;

        if (leaf) {

            while (i >= 0 && keys[i] > k) {

                keys[i + 1] = keys[i];

                i--;
            }

            keys[i + 1] = k;

            n++;
        }

        else {

            while (i >= 0 && keys[i] > k)
                i--;

            i++;

            if (child[i]->n == 2 * t - 1) {

                splitChild(i, child[i]);

                if (keys[i] < k)
                    i++;
            }

            child[i]->insertNonFull(k);
        }
    }
};

class BTree {

public:

    BTreeNode *root;

    int t;

    BTree(int degree) {

        root = nullptr;

        t = degree;
    }

    void traverse() {

        if (root != nullptr)
            root->traverse();
    }

    bool search(int k) {

        if (root == nullptr)
            return false;

        return root->search(k);
    }

    void insert(int k) {

        if (root == nullptr) {

            root = new BTreeNode(t, true);

            root->keys[0] = k;

            root->n = 1;
        }

        else {

            if (root->n == 2 * t - 1) {

                BTreeNode *s = new BTreeNode(t, false);

                s->child[0] = root;

                s->splitChild(0, root);

                int i = 0;

                if (s->keys[0] < k)
                    i++;

                s->child[i]->insertNonFull(k);

                root = s;
            }

            else {

                root->insertNonFull(k);
            }
        }
    }
};

int main() {

    BTree tree(2);

    int n, x;

    cout << "Enter number of elements: ";
    cin >> n;

    cout << "Enter elements:\n";

    for (int i = 0; i < n; i++) {

        cin >> x;

        tree.insert(x);
    }

    cout << "\nB-Tree traversal: ";

    tree.traverse();

    cout << "\nEnter element to search: ";

    cin >> x;

    if (tree.search(x))
        cout << "Element found";
    else
        cout << "Element not found";

    return 0;
}

6.	Write a program to search a pattern in a given text using the KMP algorithm.

CODE:

#include <iostream>
#include <vector>
#include <string>
using namespace std;

// Function to calculate LPS array
void computeLPS(string pattern, vector<int>& lps) {

    int m = pattern.length();

    int len = 0;

    lps[0] = 0;

    int i = 1;

    while (i < m) {

        if (pattern[i] == pattern[len]) {

            len++;

            lps[i] = len;

            i++;
        }

        else {

            if (len != 0) {

                len = lps[len - 1];
            }

            else {

                lps[i] = 0;

                i++;
            }
        }
    }
}

// KMP searching function
void KMPSearch(string text, string pattern) {

    int n = text.length();

    int m = pattern.length();

    vector<int> lps(m);

    computeLPS(pattern, lps);

    int i = 0;

    int j = 0;

    bool found = false;

    while (i < n) {

        if (text[i] == pattern[j]) {

            i++;

            j++;
        }

        if (j == m) {

            cout << "Pattern found at index "
                 << i - j << endl;

            found = true;

            j = lps[j - 1];
        }

        else if (i < n && text[i] != pattern[j]) {

            if (j != 0)
                j = lps[j - 1];

            else
                i++;
        }
    }

    if (!found)
        cout << "Pattern not found";
}

int main() {

    string text, pattern;

    cout << "Enter text: ";
    cin >> text;

    cout << "Enter pattern: ";
    cin >> pattern;

    KMPSearch(text, pattern);

    return 0;
}

7.	Write a program to implement a Suffix tree (bruteForceSuffixTrie method given in Ref. 4 at page 712).

CODE:

#include <iostream>
#include <map>
#include <string>
using namespace std;

struct Node {

    map<char, Node*> children;

    bool isEnd;

    Node() {
        isEnd = false;
    }
};

class SuffixTrie {

    Node *root;

public:

    SuffixTrie() {

        root = new Node();
    }

    // Insert a suffix
    void insertSuffix(string s) {

        Node *current = root;

        for (char ch : s) {

            if (current->children.find(ch) ==
                current->children.end()) {

                current->children[ch] = new Node();
            }

            current = current->children[ch];
        }

        current->isEnd = true;
    }

    // Construct suffix trie
    void buildSuffixTrie(string text) {

        int n = text.length();

        for (int i = 0; i < n; i++) {

            string suffix = text.substr(i);

            insertSuffix(suffix);
        }
    }

    // Search substring
    bool search(string pattern) {

        Node *current = root;

        for (char ch : pattern) {

            if (current->children.find(ch) ==
                current->children.end()) {

                return false;
            }

            current = current->children[ch];
        }

        return true;
    }
};

int main() {

    string text, pattern;

    cout << "Enter string: ";
    cin >> text;

    SuffixTrie trie;

    trie.buildSuffixTrie(text);

    cout << "Enter pattern to search: ";
    cin >> pattern;

    if (trie.search(pattern))
        cout << "Pattern found in string";
    else
        cout << "Pattern not found";

    return 0;
}

...........end......
