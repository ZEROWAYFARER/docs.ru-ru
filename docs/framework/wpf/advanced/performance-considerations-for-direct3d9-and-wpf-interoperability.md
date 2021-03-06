---
title: "Вопросы производительности, связанные с взаимодействием Direct3D9 и WPF | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "dotnet-wpf"
ms.tgt_pltfrm: ""
ms.topic: "article"
helpviewer_keywords: 
  - "Direct3D9 [взаимодействие с WPF], производительность"
  - "WPF, производительность взаимодействия Direct3D9"
ms.assetid: ea8baf91-12fe-4b44-ac4d-477110ab14dd
caps.latest.revision: 19
author: "dotnet-bot"
ms.author: "dotnetcontent"
manager: "wpickett"
caps.handback.revision: 19
---
# Вопросы производительности, связанные с взаимодействием Direct3D9 и WPF
Содержимое Direct3D9 можно разместить с помощью класса <xref:System.Windows.Interop.D3DImage>.  Размещение содержимого Direct3D9 может повлиять на производительность приложения.  В данном разделе приводятся рекомендации по оптимизации производительности при размещении содержимого Direct3D9 в WPF\-приложении.  Сюда входят рекомендации по использованию класса <xref:System.Windows.Interop.D3DImage> и рекомендации, связанные с использованием Windows Vista, Windows XP и нескольких мониторов.  
  
> [!NOTE]
>  Примеры кода, в которых демонстрируется использование этих рекомендаций, см. в разделе [Взаимодействие WPF и Direct3D9](../../../../docs/framework/wpf/advanced/wpf-and-direct3d9-interoperation.md).  
  
## Используйте D3DImage осторожно  
 Содержимое Direct3D9, размещенное в объекте <xref:System.Windows.Interop.D3DImage>, отображается не так быстро, как в чистом Direct3D\-приложении.  Копирование поверхности и очистка буфера команд могут оказаться весьма ресурсоемкими операциями.  По мере увеличения количества экземпляров <xref:System.Windows.Interop.D3DImage> увеличивается и количество операций очистки буфера, что приводит к снижению производительности.  Следовательно, класс <xref:System.Windows.Interop.D3DImage> следует использовать осторожно.  
  
## Рекомендации для Windows Vista  
 Для обеспечения максимальной производительности в системе Windows Vista с дисплеем, настроенным на использование модели драйверов WDDM, создайте поверхность Direct3D9 на устройстве `IDirect3DDevice9Ex`.  Это позволит совместно использовать поверхность.  Видеоадаптер должен поддерживать возможности драйверов Windows Vista `D3DDEVCAPS2_CAN_STRETCHRECT_FROM_TEXTURES` и `D3DCAPS2_CANSHARERESOURCE`.  При использовании других параметров поверхность копируется программно, что приводит к существенному снижению производительности.  
  
> [!NOTE]
>  Если в Windows Vista используется дисплей, настроенный на использование модели драйверов XDDM Windows XP, поверхность всегда копируется программно, независимо от установленных параметров.  При установке надлежащих параметров и наличии подходящего видеоадаптера производительность в Windows Vista при использовании модели WDDM будет выше, поскольку копирование поверхностей выполняется аппаратно.  
  
## Рекомендации для Windows XP  
 Для обеспечения максимальной производительности в системе Windows XP, в которой используется модель драйверов XDDM, создайте блокируемую поверхность, которая правильно работает при вызове метода `IDirect3DSurface9::GetDC`.  Во внутренней реализации метод `BitBlt` аппаратно переносит поверхность между устройствами.  Метод `GetDC` всегда работает на поверхностях XRGB.  Однако на клиентских компьютерах под управлением Windows XP с пакетом обновления 2 или 3 \(SP2 или SP3\), на которых установлено исправление для функции многоуровневых окон, данный метод работает только на поверхностях ARGB.  Видеоадаптер должен поддерживать возможность драйвера `D3DDEVCAPS2_CAN_STRETCHRECT_FROM_TEXTURES`.  
  
 16\-разрядная глубина цвета на рабочем столе может привести к существенному снижению производительности.  Рекомендуется использовать 32\-разрядную глубину цвета.  
  
 При разработке приложения, предназначенного как для Windows Vista, так и для Windows XP, тестируйте производительность в Windows XP.  В Windows XP имеется проблема нехватки видеопамяти.  Кроме того, объект <xref:System.Windows.Interop.D3DImage> в Windows XP использует больше памяти и пропускной способности, чем в модели WDDM Windows Vista, из\-за необходимости копирования дополнительной видеопамяти.  Таким образом, при использовании одинакового видеооборудования производительность в Windows XP, скорее всего, будет ниже, чем в Windows Vista.  
  
> [!NOTE]
>  Модель XDDM доступна как в Windows XP, так и в Windows Vista; однако модель WDDM доступна только в Windows Vista.  
  
## Общие рекомендации  
 При создании устройства используйте флаг создания `D3DCREATE_MULTITHREADED`.  Это снижает производительность, однако система отрисовки WPF вызывает методы для этого устройства из другого потока.  Убедитесь в том, что протокол блокировки используется правильно, чтобы исключить одновременный доступ к устройству двух потоков.  
  
 Если отрисовка выполняется в управляемом потоке WPF, настоятельно рекомендуется создавать устройство с флагом создания `D3DCREATE_FPU_PRESERVE`.  Если этот параметр не установлен, то при визуализации D3D может снизиться точность операций WPF двойной точности, что может привести к проблемам с отрисовкой.  
  
 Заполнение объекта <xref:System.Windows.Interop.D3DImage> выполняется быстро, за исключением заполнения неквадратичной поверхности без аппаратной поддержки либо заполнения объектов <xref:System.Windows.Media.DrawingBrush> и <xref:System.Windows.Media.VisualBrush>, которые содержат объект <xref:System.Windows.Interop.D3DImage>.  
  
## Рекомендации при использовании нескольких мониторов  
 При использовании компьютера с несколькими мониторами необходимо следовать приведенным выше рекомендациям.  Существуют также дополнительные рекомендации, связанные с использованием нескольких мониторов.  
  
 При создании заднего буфера он создается для конкретного устройства и адаптера, однако кадровый буфер WPF позволяет выводить на любой адаптер.  Операция копирования данных между адаптерами с целью обновления кадрового буфера может быть весьма ресурсоемкой.  Если в системе Windows Vista, настроенной на использование модели WDDM с несколькими видеоадаптерами и устройством `IDirect3DDevice9Ex` кадровый буфер расположен на другом адаптере, но на том же видеоадаптере, производительность не снижается.  Однако в системе Windows XP при использовании модели XDDM с несколькими видеоадаптерами производительность существенно снижается, если кадровый буфер и задний буфер отображаются на разных адаптерах.  Дополнительные сведения см. в разделе [Взаимодействие WPF и Direct3D9](../../../../docs/framework/wpf/advanced/wpf-and-direct3d9-interoperation.md).  
  
## Сводка по производительности  
 В приведенной ниже таблице показана производительность при обновлении кадрового буфера как функция операционной системы, формата пикселей и возможности блокировки поверхности.  Предполагается, что кадровый буфер и задний буфер расположены на одном адаптере.  В зависимости от конфигурации адаптера аппаратное обновление, как правило, выполняется гораздо быстрее программного.  
  
|Формат пикселей поверхности|Windows Vista, WDDM и 9Ex|Другие конфигурации Windows Vista|Windows XP с пакетом обновления 3 \(SP3\) или пакетом обновления 2 \(SP2\) и установленным исправлением|Windows XP с пакетом обновления 2 \(SP2\)|  
|---------------------------------|-------------------------------|---------------------------------------|-------------------------------------------------------------------------------------------------------------|-----------------------------------------------|  
|D3DFMT\_X8R8G8B8 \(без возможности блокировки\)|**Аппаратное обновление**|Программное обновление|Программное обновление|Программное обновление|  
|D3DFMT\_X8R8G8B8 \(с возможностью блокировки\)|**Аппаратное обновление**|Программное обновление|**Аппаратное обновление**|**Аппаратное обновление**|  
|D3DFMT\_A8R8G8B8 \(без возможности блокировки\)|**Аппаратное обновление**|Программное обновление|Программное обновление|Программное обновление|  
|D3DFMT\_A8R8G8B8 \(с возможностью блокировки\)|**Аппаратное обновление**|Программное обновление|**Аппаратное обновление**|Программное обновление|  
  
## См. также  
 <xref:System.Windows.Interop.D3DImage>   
 [Взаимодействие WPF и Direct3D9](../../../../docs/framework/wpf/advanced/wpf-and-direct3d9-interoperation.md)   
 [Пошаговое руководство. Создание содержимого Direct3D9 для размещения в WPF](../../../../docs/framework/wpf/advanced/walkthrough-creating-direct3d9-content-for-hosting-in-wpf.md)   
 [Пошаговое руководство. Размещение содержимого Direct3D9 в WPF](../../../../docs/framework/wpf/advanced/walkthrough-hosting-direct3d9-content-in-wpf.md)