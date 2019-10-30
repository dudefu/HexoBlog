---
title: wordcount求学生平均成绩案例
categories: 大数据
tags: hadoop
abbrlink: f01322d1
date: 2018-03-25 14:24:09
---
```Java
package com.Practice.AverageScores;

import com.Practice.SameFriend.SameFriend;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
```
<!---more--->
```
import java.io.IOException;
import java.util.StringTokenizer;
/**
 * 求学生平均成绩
 * 计算学生考试平均成绩 源数据：
     张三 98
     李四 96
     王五 95
     张三 90
     李四 92
     王五 99
     张三 80
     李四 90
     王五 94
     张三 82
     李四 92
 */
public class AverageScores {
    public static void main(String[] args) throws IOException, ClassNotFoundException, InterruptedException {
        Configuration conf = new Configuration();
        FileSystem fs = FileSystem.get(conf);

        Job job = Job.getInstance(conf);
        job.setJar("wordcountJar/wordcount.jar");

        job.setMapperClass(AverageScoresMapper.class);
        job.setReducerClass(AverageScoresReducer.class);

        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);

        Path inputPath = new Path("input/AverageScore");
        Path outputPath = new Path("output/AverageScore");


        if(fs.isDirectory(outputPath)){
            fs.delete(outputPath,true);
        }
        FileInputFormat.setInputPaths(job,inputPath);
        FileOutputFormat.setOutputPath(job,outputPath);

        boolean waitForCompletion = job.waitForCompletion(true);
        System.exit(waitForCompletion ? 0 : 1 );
    }

    public static class AverageScoresMapper extends Mapper<LongWritable,Text,Text,IntWritable> {
        private Text outKey = new Text();
        private IntWritable outValue = new IntWritable();
        @Override
        protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
            String[] splits = value.toString().split("\t");
            outKey.set(splits[0]);
            outValue.set(Integer.parseInt(splits[1]));
            context.write(outKey,outValue);
        }
    }

    public static class AverageScoresReducer extends Reducer<Text,IntWritable,Text,IntWritable> {
        private IntWritable outValue = new IntWritable();
        @Override
        protected void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException, InterruptedException {
            int avg = 0 ;
            int sum = 0 ;
            int count = 0 ;
            for (IntWritable val :
                    values) {
                int score = val.get();
                sum += score ;
                count ++ ;
            }
            avg = sum /count ;
            outValue.set(avg);
            context.write(key,outValue);
        }
    }

}

```