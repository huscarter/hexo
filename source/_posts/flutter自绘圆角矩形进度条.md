---
title: flutter自绘圆角矩形进度条
date: 2020-05-13 20:29:44
categories: flutter
tags:
     - flutter
description: 前阵子应交互同学要求编写了一个类似微信文件发送的进度条，效果图如下正文。因为第一次通过flutter自绘图形，里面有些点还是很必要记录的。
---


### 实现思路
要实现此效果我们需要3步，第一步画一个扇形展示进度，第二步画一个圆角正方形做外围，第三步将扇形放入到圆角正方形里。

1. 画一个扇形，用来表示进度。
这里的扇形需要注意三点：
- 扇形的半径是圆角正方形斜边的一半，即(宽幂+高幂)开根号。因为扇形的圆需要为圆角正方形的外切圆，否则扇形不能覆盖整个圆角正方形。
- 扇形大小和位置有drawArc的第一个参数控制（Rect），此Rect为矩形设置位置和大小之后扇形即为此矩形的内切圆。
- 按照需求圆角正方是一开始是实心的，有进度时出现了扇形缺口，所以初始值时100%，然后递减，上传完成之后进度为0；
```
/// 用于画扇形
/// 
/// rect：控制扇形位置和大小的矩形，扇形圆为此矩形的内切圆
/// startAngle：扇形开位置
/// sweepAngle：扇形的弧度
/// useCenter：是否为实心
/// paint：画笔
Canva.drawArc(Rect rect, double startAngle, double sweepAngle, bool useCenter, Paint paint);
```

2. 画一个圆角正方形。
矩形的尺寸就是设计同学设计的大小，此处要注意矩形的的位置取决于扇形的位置。扇形圆为圆角正方形的内切圆，而圆角正方形的位置由左上角的坐标确定。扇形的控制矩形左上角为原点，推到出来圆角正方是的左上角坐标为(wrapWidth/2-arcWidth/2,wrapWidth/2-arcWidth/2);其中wrapWidth为扇形控制矩形的宽度，arcWidth为圆角矩形的宽度。
```
/// 目前dart库的Canvas提供直接使用的方法
/// 
/// 最后一个参数就是圆角弧度
Canvas.clipRRect(RRect.fromLTRBR(double left, double top, double right, double bottom,Radius radius));
```

### 下面是实现类
```
import 'dart:math' as math;
import 'package:flutter/foundation.dart';
import 'package:flutter/material.dart';
import 'package:flutter/widgets.dart';

/// 扇形进度条
class RRectProgressIndicator extends StatefulWidget {
  final double arcWidth;

  final double value;

  final Color backgroundColor;

  /// [ThemeData.accentColor].
  final Animation<Color> valueColor;

  final String semanticsLabel;

  final String semanticsValue;

  Color _getValueColor(BuildContext context) =>
      valueColor?.value ?? Theme.of(context).accentColor;

  @override
  void debugFillProperties(DiagnosticPropertiesBuilder properties) {
    super.debugFillProperties(properties);
    properties.add(PercentProperty('value', value,
        showName: false, ifNull: '<indeterminate>'));
  }

  const RRectProgressIndicator({
    Key key,
    this.value,
    this.backgroundColor,
    this.strokeWidth = 4.0,
    this.arcWidth = 80,
    this.valueColor,
    this.semanticsLabel,
    this.semanticsValue,
  }) : super(key: key);

  /// The width of the line used to draw the circle.
  final double strokeWidth;

  @override
  _RRectProgressIndicatorState createState() => _RRectProgressIndicatorState();
}

class _RRectProgressIndicatorState extends State<RRectProgressIndicator>
    with SingleTickerProviderStateMixin {
  AnimationController _controller;

  ///
  double wrapWidth;

  @override
  void initState() {
    super.initState();

    wrapWidth = math.sqrt2 * widget.arcWidth;

    _controller = AnimationController(
      duration: const Duration(seconds: 5),
      vsync: this,
    );
    if (widget.value == null) _controller.repeat();
  }

  @override
  void didUpdateWidget(RRectProgressIndicator oldWidget) {
    super.didUpdateWidget(oldWidget);
    if (widget.value == null && !_controller.isAnimating)
      _controller.repeat();
    else if (widget.value != null && _controller.isAnimating)
      _controller.stop();
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Container(
      width: widget.arcWidth,
      height: widget.arcWidth,
      child: CustomPaint(
        painter: _RRectProgressIndicatorPainter(
          backgroundColor: widget.backgroundColor,
          valueColor: widget._getValueColor(context),
          value: widget.value,
          strokeWidth: widget.strokeWidth,
          arcWidth: widget.arcWidth,
          wrapWidth: wrapWidth,
        ),
      ),
    );
  }
}

class _RRectProgressIndicatorPainter extends CustomPainter {
  _RRectProgressIndicatorPainter({
    this.backgroundColor,
    this.valueColor,
    this.value,
    this.headValue,
    this.tailValue,
    this.stepValue,
    this.arcWidth,
    this.wrapWidth,
    this.rotationValue,
    this.strokeWidth,
  })  : arcStart = value != null
            ? _startAngle
            : _startAngle +
                tailValue * 3 / 2 * math.pi +
                rotationValue * math.pi * 1.7 -
                stepValue * 0.8 * math.pi,
        arcSweep = value != null
            ? value.clamp(0.0, 1.0) * _sweep
            : math.max(
                headValue * 3 / 2 * math.pi - tailValue * 3 / 2 * math.pi,
                _epsilon),
        position = wrapWidth / 2 - arcWidth / 2;

  final Color backgroundColor;
  final Color valueColor;
  final double value;
  final double headValue;
  final double tailValue;
  final int stepValue;
  final double rotationValue;
  final double strokeWidth;
  final double arcStart;
  final double arcSweep;
  final double arcWidth;
  final double wrapWidth;
  final double position;

  static const double _twoPi = math.pi * 2.0;
  static const double _epsilon = .001;

  // Canvas.drawArc(r, 0, 2*PI) doesn't draw anything, so just get close.
  static const double _sweep = _twoPi - _epsilon;
  static const double _startAngle = -math.pi / 2.0;

  @override
  void paint(Canvas canvas, Size size) {
    // 圆角矩形限制
    canvas.clipRRect(RRect.fromLTRBR(position, position, position + arcWidth,
        position + arcWidth, Radius.circular(5)));
    // 画笔设置
    final Paint paint = Paint()
      ..color = valueColor
      ..strokeWidth = strokeWidth
      ..style = PaintingStyle.fill;
    // 制画
    canvas.drawArc(Offset.zero & Size(wrapWidth, wrapWidth),
        arcSweep + _startAngle, _twoPi - arcSweep, true, paint);
  }

  @override
  bool shouldRepaint(_RRectProgressIndicatorPainter oldPainter) {
    return oldPainter.backgroundColor != backgroundColor ||
        oldPainter.valueColor != valueColor ||
        oldPainter.value != value ||
        oldPainter.headValue != headValue ||
        oldPainter.tailValue != tailValue ||
        oldPainter.stepValue != stepValue ||
        oldPainter.rotationValue != rotationValue ||
        oldPainter.strokeWidth != strokeWidth;
  }
}
```
