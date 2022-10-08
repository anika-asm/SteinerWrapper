import java.io.InputStreamReader;
import java.util.*;
import java.io.*;

/*
 * Finds the # of components and # of crossings for a subgrid of a graph. 
 * 
 * Algorithm: 
 *  1. Use DFS with coloring to find # of components.
 *  2. While doing DFS, check for each node if their connected node   
 */
public class Wrapper {
    private static int numCrossing = 0;

    public static void main(String[] args) throws IOException {
        // Read in input
        BufferedReader br = new BufferedReader(new FileReader("input.txt"));

        int numNodes = Integer.parseInt(br.readLine());

        int[] quad = new int[numNodes + 1]; // Stores quadrant of each node. 1 is the top right quadrant, 2 is bottom
                                            // right, 3 is bottom left, 4 is top left. Can fix this later if you want.

        StringTokenizer st = new StringTokenizer(br.readLine());
        for (int i = 0; i <= numNodes; i++) {
            // Ignore 0 for 1-based indexing (no 0 node)
            if (i != 0) {
                quad[i] = Integer.parseInt(st.nextToken());
            }
        }

        ArrayList<ArrayList<Integer>> adjList = new ArrayList<>(); // Ex: {0: {5, 4, 3}, 1: {2, 3, 8}, 2: {3, 5, 10}}

        for (int i = 0; i <= numNodes; i++) {
            adjList.add(new ArrayList<Integer>());

            // i = 0 doesn't correspond to a node
            if (i == 0)
                continue;

            StringTokenizer s = new StringTokenizer(br.readLine());

            while (s.hasMoreTokens()) {
                adjList.get(i).add(Integer.parseInt(s.nextToken()));
            }
        }

        System.out.println("numNodes: " + numNodes);
        System.out.println("quadrants: " + Arrays.toString(quad));
        System.out.println("adjacency list: " + adjList);

        int color = 1;
        int[] visited = new int[numNodes + 1];

        // Do dfs on all the nodes
        int currQuad = 1;
        for (int i = 1; i <= 16; i++) {
            if (visited[i] == 0) {
                dfs(adjList, quad[i], quad, visited, i, color);
                color += 1; // Increment the color for the next component
            }

            // If you've visited all the nodes in this quadrant, then move to the next one.
            if (i % 4 == 0) {
                int numComponents = color - 1; // Because of extra increment above
                System.out.println(
                        "currQuad: " + currQuad + " numComponents: " + numComponents + " numCrossing: " + numCrossing);
                color = 1; // Reset color for next quadrant
                numCrossing = 0;
                currQuad++; // Increment which quadrant we are at
            }
        }
    }

    private static void dfs(ArrayList<ArrayList<Integer>> adjList, int startingQuad, int[] quad, int[] visited,
            int currNode, int color) {
        // If this node has already been visisted then ignore it. Also ignore it if the
        // node is in a different quadrant than where we are doing dfs in.
        if (startingQuad != quad[currNode]) {
            numCrossing++;
            return;
        }

        if (visited[currNode] != 0) {
            return;
        }

        // Assign the color
        visited[currNode] = color;

        for (int node : adjList.get(currNode)) {
            dfs(adjList, startingQuad, quad, visited, node, color);
        }
    }
}
