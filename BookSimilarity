package org.example;

import java.io.*;
import java.nio.file.*;
import java.util.*;
import java.util.concurrent.*;
import java.util.stream.*;

public class BookSimilarity {

    public static void main(String[] args) {

        String directoryPath = "C:\\Books"; // you have to write your own directory path. || kendi dosya yolunu yazmalısın.

        File dir = new File(directoryPath);
        if (!dir.isDirectory()) {
            System.err.println("Dosya Yolunu Yanlış Girdiniz!!");
            System.exit(1);
        }

        File[] files = dir.listFiles((d, name) -> name.endsWith(".txt"));
        if (files == null || files.length == 0) {
            System.err.println("Dosya Boş!!");
            System.exit(1);
        }

        ExecutorService executor = Executors.newFixedThreadPool(files.length);
        List<Future<Map<String, Integer>>> futures = new ArrayList<>();

        for (File file : files) {
            futures.add(executor.submit(() -> countWords(file)));
        }

        List<Map<String, Integer>> wordCounts = new ArrayList<>();
        for (Future<Map<String, Integer>> future : futures) {
            try {
                wordCounts.add(future.get());
            } catch (InterruptedException | ExecutionException e) {
                e.printStackTrace();
            }
        }

        executor.shutdown();

        Set<String> allWords = wordCounts.stream()
                .flatMap(map -> map.keySet().stream())
                .collect(Collectors.toSet());

        int totalCommonWords = 0;
        for (String word : allWords) {
            boolean isCommon = wordCounts.stream().allMatch(map -> map.containsKey(word));
            if (isCommon) {
                totalCommonWords++;
            }
        }

        double similarity = calculateSimilarity(wordCounts, allWords);

        System.out.println("Toplam Ortak Kelime Sayisi: " + totalCommonWords);
        System.out.printf("Benzerlik Oranı: %.2f%%%n", similarity * 100);
    }

    private static Map<String, Integer> countWords(File file) {
        Map<String, Integer> wordCount = new HashMap<>();
        try (BufferedReader reader = Files.newBufferedReader(file.toPath())) {
            String line;
            while ((line = reader.readLine()) != null) {
                String[] words = line.toLowerCase().replaceAll("[^a-zA-Z0-9]", " ").split("\\s+");
                for (String word : words) {
                    if (!word.isEmpty()) {
                        wordCount.put(word, wordCount.getOrDefault(word, 0) + 1);
                    }
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        return wordCount;
    }

    private static double calculateSimilarity(List<Map<String, Integer>> wordCounts, Set<String> allWords) {
        double totalSimilarity = 0;

        for (String word : allWords) {
            double wordSimilarity = 1.0;

            for (Map<String, Integer> wordCount : wordCounts) {
                int count = wordCount.getOrDefault(word, 0);
                wordSimilarity *= (double) count / (count + 1);
            }

            totalSimilarity += wordSimilarity;
        }

        return totalSimilarity / allWords.size();
    }
}
