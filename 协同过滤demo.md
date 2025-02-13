
协同过滤（Collaborative Filtering）是一种推荐系统算法，它基于用户对物品的评分或行为来预测用户可能感兴趣的其他物品。协同过滤算法可以分为两大类：基于用户的（User-based）和基于物品的（Item-based）。下面我将分别介绍这两种方法的基本实现思路，并给出Java代码示例。
1. 基于用户的协同过滤（User-based Collaborative Filtering）
基于用户的协同过滤算法首先找到与目标用户具有相似喜好的其他用户，然后推荐这些相似用户喜欢的物品给目标用户。
步骤：
1.	计算用户之间的相似度。
2.	找到与目标用户相似度最高的N个用户。
3.	根据这些相似用户的评分来预测目标用户对未评分物品的评分。
4.	推荐预测评分最高的物品给目标用户。
Java代码示例：
```java
import java.util.*;

public class UserBasedCollaborativeFiltering {

    static class UserItemRating {
        int userId;
        int itemId;
        double rating;

        public UserItemRating(int userId, int itemId, double rating) {
            this.userId = userId;
            this.itemId = itemId;
            this.rating = rating;
        }
    }

    // 计算余弦相似度
    static double cosineSimilarity(double[] vectorA, double[] vectorB) {
        double dotProduct = 0.0;
        double normA = 0.0;
        double normB = 0.0;
        for (int i = 0; i < vectorA.length; i++) {
            dotProduct += vectorA[i] * vectorB[i];
            normA += Math.pow(vectorA[i], 2);
            normB += Math.pow(vectorB[i], 2);
        }
        return dotProduct / (Math.sqrt(normA) * Math.sqrt(normB));
    }

    // 为用户推荐物品
    public List<Integer> recommendItems(int targetUserId, List<UserItemRating> ratings, int topN) {
        Map<Integer, double[]> userRatings = new HashMap<>();
        Map<Integer, Set<Integer>> userItemMap = new HashMap<>();

        // 构建用户评分矩阵
        for (UserItemRating rating : ratings) {
            userRatings.computeIfAbsent(rating.userId, k -> new double[ratings.size()]);
            userRatings.get(rating.userId)[rating.itemId] = rating.rating;

            userItemMap.computeIfAbsent(rating.userId, k -> new HashSet<>());
            userItemMap.get(rating.userId).add(rating.itemId);
        }

        // 计算目标用户与其他用户的相似度
        Map<Integer, Double> similarityScores = new HashMap<>();
        for (Map.Entry<Integer, double[]> userVector : userRatings.entrySet()) {
            int otherUserId = userVector.getKey();
            if (otherUserId == targetUserId) continue;

            double similarity = cosineSimilarity(userRatings.get(targetUserId), userVector.getValue());
            similarityScores.put(otherUserId, similarity);
        }

        // 找到最相似的用户
        List<Map.Entry<Integer, Double>> sortedSimilarities = similarityScores.entrySet().stream()
                .sorted(Map.Entry.<Integer, Double>comparingByValue().reversed())
                .limit(topN)
                .collect(Collectors.toList());

        // 预测评分并推荐物品
        Map<Integer, Double> predictions = new HashMap<>();
        for (Map.Entry<Integer, Double> similarUser : sortedSimilarities) {
            int similarUserId = similarUser.getKey();
            double similarity = similarUser.getValue();

            for (Integer itemId : userItemMap.get(similarUserId)) {
                if (!userItemMap.get(targetUserId).contains(itemId)) {
                    double weightedRating = similarity * userRatings.get(similarUserId)[itemId];
                    predictions.merge(itemId, weightedRating, Double::sum);
                }
            }
        }

        // 根据预测评分排序并返回TopN推荐
        return predictions.entrySet().stream()
                .sorted(Map.Entry.<Integer, Double>comparingByValue().reversed())
                .limit(topN)
                .map(Map.Entry::getKey)
                .collect(Collectors.toList());
    }
}
1. 基于物品的协同过滤（Item-based Collaborative Filtering）
基于物品的协同过滤算法首先找到与目标物品相似的物品，然后推荐这些相似物品给喜欢目标物品的用户。
步骤：
1.	计算物品之间的相似度。
2.	根据用户对物品的评分，找到用户喜欢的物品。
3.	推荐与用户喜欢的物品相似度最高的N个物品。
Java代码示例：
java复制
import java.util.*;

public class ItemBasedCollaborativeFiltering {

    static class UserItemRating {
        int userId;
        int itemId;
        double rating;

        public UserItemRating(int userId, int itemId, double rating) {
            this.userId = userId;
            this.itemId = itemId;
            this.rating = rating;
        }
    }

    // 计算余弦相似度
    static double cosineSimilarity(double[] vectorA, double[] vectorB) {
        double dotProduct = 0.0;
        double normA = 0.0;
        double normB = 0.0;
        for (int i = 0; i < vectorA.length; i++) {
            dotProduct += vectorA[i] * vectorB[i];
            normA += Math.pow(vectorA[i], 2);
            normB += Math.pow(vectorB[i], 2);
        }
        return dotProduct / (Math.sqrt(normA) * Math.sqrt(normB));
    }

    // 为用户推荐物品
    public List<Integer> recommendItems(int targetUserId, List<UserItemRating> ratings, int topN) {
        Map<Integer, double[]> itemRatings = new HashMap<>();
        Map<Integer, Set<Integer>> itemUserMap = new HashMap<>();

        // 构建物品评分矩阵
        for (UserItemRating rating : ratings) {
            itemRatings.computeIfAbsent(rating.itemId, k -> new double[ratings.size()]);
            itemRatings.get(rating.itemId)[rating.userId] = rating.rating;

            itemUserMap.computeIfAbsent(rating.itemId, k -> new HashSet<>());
            itemUserMap.get(rating.itemId).add(rating.userId);
        }

        // 计算物品之间的相似度
        Map<Integer, Map<Integer, Double>> itemSimilarities = new HashMap<>();
        for (Map.Entry<Integer, double[]> itemVector : itemRatings.entrySet()) {
            int itemId1 = itemVector.getKey();
            itemSimilarities.put(itemId1, new HashMap<>());

            for (Map.Entry<Integer, double[]> otherItemVector : itemRatings.entrySet()) {
                int itemId2 = otherItemVector.getKey();
                if (itemId1 == itemId2) continue;

                double similarity = cosineSimilarity(itemVector.getValue(), otherItemVector.getValue());
                itemSimilarities.get(itemId1).put(itemId2, similarity);
            }
        }

        // 找到用户喜欢的物品
        Set<Integer> likedItems = itemUserMap.entrySet().stream()
                .filter(entry -> entry.getValue().contains(targetUserId))
                .map(Map.Entry::getKey)
                .collect(Collectors.toSet());

        // 预测评分并推荐物品
        Map<Integer, Double> predictions = new HashMap<>();
        for (Integer likedItemId : likedItems) {
            for (Map.Entry<Integer, Double> similarItem : itemSimilarities.get(likedItemId).entrySet()) {
                int similarItemId = similarItem.getKey();
                double similarity = similarItem.getValue();

                if (!likedItems.contains(similarItemId)) {
                    predictions.merge(similarItemId, similarity, Double::sum);
                }
            }
        }

        // 根据预测评分排序并返回TopN推荐
        return predictions.entrySet().stream()
                .sorted(Map.Entry.<Integer, Double>comparingByValue().reversed())
                .limit(topN)
                .map(Map.Entry::getKey)
                .collect(Collectors.toList());
    }
}
注意事项：
1.	数据预处理：在实际应用中，需要对数据进行预处理，如处理缺失值、标准化评分等。
2.	性能优化：对于大规模数据，可以考虑使用矩阵分解等方法优化性能。
3.	模型评估：使用适当的评估指标（如准确率、召回率、F1分数等）来评估推荐系统的性能。

 
//单测
import java.util.HashMap;
import java.util.Map;

public class CollaborativeFiltering {

    // 用户评分矩阵
    private static final Map<String, Map<String, Integer>> ratings = new HashMap<>();

    static {
        ratings.put("UserA", Map.of("Item1", 5, "Item2", 3, "Item4", 1));
        ratings.put("UserB", Map.of("Item1", 4, "Item4", 1));
        ratings.put("UserC", Map.of("Item2", 1, "Item4", 5));
        ratings.put("UserD", Map.of("Item3", 5, "Item4", 4));
    }

    // 计算余弦相似度
    private double cosineSimilarity(Map<String, Integer> ratings1, Map<String, Integer> ratings2) {
        double dotProduct = 0.0;
        double normA = 0.0;
        double normB = 0.0;

        for (String item : ratings1.keySet()) {
            if (ratings2.containsKey(item)) {
                dotProduct += ratings1.get(item) * ratings2.get(item);
            }
            normA += Math.pow(ratings1.get(item), 2);
        }

        for (double rating : ratings2.values()) {
            normB += Math.pow(rating, 2);
        }

        normA = Math.sqrt(normA);
        normB = Math.sqrt(normB);

        return (normA == 0 || normB == 0) ? 0 : dotProduct / (normA * normB);
    }

    // 为用户推荐物品
    public Map<String, Double> recommendItems(String user) {
        Map<String, Integer> userRatings = ratings.get(user);
        Map<String, Double> scoreMap = new HashMap<>();

        for (String otherUser : ratings.keySet()) {
            if (!otherUser.equals(user)) {
                double similarity = cosineSimilarity(userRatings, ratings.get(otherUser));

                for (String item : ratings.get(otherUser).keySet()) {
                    if (!userRatings.containsKey(item)) {
                        scoreMap.put(item, scoreMap.getOrDefault(item, 0.0) + similarity * ratings.get(otherUser).get(item));
                    }
                }
            }
        }

        return scoreMap;
    }

//    public static void main(String[] args) {
//        CollaborativeFiltering cf = new CollaborativeFiltering();
//        Map<String, Double> recommendations = cf.recommendItems("UserA");
//
//        System.out.println("推荐物品给 UserA: " + recommendations);
//    }
}
```

