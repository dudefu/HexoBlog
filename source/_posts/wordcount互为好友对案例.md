---
title: wordcount 互为好友对案例
categories: 大数据
tags: mapreduce
abbrlink: b861840c
date: 2018-03-25 14:20:41
---
```Java
package com.Practice.SameFriend2;

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

/**
 * 求互粉好友对 比如：如果A和B互为好友，那么A-B即为互粉好友对。
 *   A:B,C,D,F,E,O
     B:A,C,E,K
     C:F,A,D,I
     D:A,E,F,L
     E:B,C,D,M,L
     F:A,B,C,D,E,O,M
     G:A,C,D,E,F
     H:A,C,D,E,O
     I:A,O
     J:B,O
     K:A,C,D
     L:D,E,F
     M:E,F,G
     O:A,H,I,J,K
 */
public class SameFriend2 {

    public static void main(String[] args) throws IOException, ClassNotFoundException, InterruptedException {
        Configuration conf = new Configuration();
        FileSystem fs = FileSystem.get(conf);

        Job job = Job.getInstance(conf);
        job.setJar("wordcountJar/wordcount.jar");

        job.setMapperClass(SameFriend2Mapper.class);
        job.setReducerClass(SameFriend2Reducer.class);

        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);

        Path inputPath = new Path("input/SameFriend2");
        Path outputPath = new Path("output/SameFriend2");


        if(fs.isDirectory(outputPath)){
            fs.delete(outputPath,true);
        }
        FileInputFormat.setInputPaths(job,inputPath);
        FileOutputFormat.setOutputPath(job,outputPath);

        boolean waitForCompletion = job.waitForCompletion(true);
        System.exit(waitForCompletion ? 0 : 1 );
    }

    public static class SameFriend2Mapper extends Mapper<LongWritable,Text,Text,IntWritable> {
        private Text outKey = new Text();
        private IntWritable outValue = new IntWritable(1);
        @Override
        protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
            String[] splits = value.toString().split(":");
            String[] strings = splits[1].split(",");
            for (String str:
                 strings) {
                //关键代码：如果A和B互为好友，则必定存在A-B和B-A，此处的作用就是将A-B和B-A 都编程为A-B，或者都编程为B-A
                if(splits[0].compareTo(str)>0){
                    outKey.set(str+"-"+splits[0]);
                }else{
                    outKey.set(splits[0]+"-"+str);
                }
               context.write(outKey,outValue);
            }
        }
    }

    /**
     * 如果互为好友对，count必定为2
     */
    public static class SameFriend2Reducer extends Reducer<Text,IntWritable,Text,IntWritable> {
        private IntWritable outValue = new IntWritable();
        @Override
        protected void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException, InterruptedException {
            int count = 0 ;
            for (IntWritable val :
                    values) {
                count += val.get();
            }
            //当count为2时，即可求出好友对
            if(count == 2){
                outValue.set(count);
                context.write(key,outValue);
            }
        }
    }
}

```
```