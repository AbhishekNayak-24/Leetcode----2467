# Leetcode----2467
Most Profitable Path in a Tree
//code in java 
import java.util.*;

class Solution {
    public int mostProfitablePath(int[][] edges, int bob, int[] amount) {
        int n = amount.length;
        List<Integer>[] graph = new ArrayList[n];

        // Initialize adjacency list
        for (int i = 0; i < n; i++) {
            graph[i] = new ArrayList<>();
        }

        // Build the undirected graph
        for (int[] edge : edges) {
            graph[edge[0]].add(edge[1]);
            graph[edge[1]].add(edge[0]);
        }

        // Find shortest path from node 0 to all nodes
        int[] shortestDist = new int[n];
        Arrays.fill(shortestDist, -1);
        shortestDist[0] = 0;
        
        Queue<Integer> queue = new LinkedList<>();
        queue.offer(0);

        while (!queue.isEmpty()) {
            int node = queue.poll();
            for (int neighbor : graph[node]) {
                if (shortestDist[neighbor] == -1) {
                    shortestDist[neighbor] = shortestDist[node] + 1;
                    queue.offer(neighbor);
                }
            }
        }

        // Track Bob's path
        int[] bobDist = new int[n];
        Arrays.fill(bobDist, -1);
        bobDist[bob] = 0;
        
        int curr = bob;
        while (curr != 0) {
            for (int neighbor : graph[curr]) {
                if (shortestDist[neighbor] == shortestDist[curr] - 1) {
                    bobDist[neighbor] = bobDist[curr] + 1;
                    curr = neighbor;
                    break;
                }
            }
        }

        // DFS to find the maximum profit path
        return dfs(0, -1, 0, graph, amount, shortestDist, bobDist);
    }

    private int dfs(int node, int parent, int depth, List<Integer>[] graph, int[] amount, int[] shortestDist, int[] bobDist) {
        int profit;
        
        if (bobDist[node] == -1 || depth < bobDist[node]) {
            profit = amount[node]; // Alice takes full amount
        } else if (depth == bobDist[node]) {
            profit = amount[node] / 2; // Split with Bob
        } else {
            profit = 0; // Bob already took the amount
        }

        int maxProfit = Integer.MIN_VALUE;
        boolean isLeaf = true;

        for (int neighbor : graph[node]) {
            if (neighbor != parent) {
                isLeaf = false;
                maxProfit = Math.max(maxProfit, dfs(neighbor, node, depth + 1, graph, amount, shortestDist, bobDist));
            }
        }

        return profit + (isLeaf ? 0 : maxProfit);
    }
}
