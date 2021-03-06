if(NOT CMAKE_SYSTEM_NAME STREQUAL WASI)
list(APPEND FOUNDATION_WASI_UNAVAILABLE_SOURCES
  FileManager+POSIX.swift
  FileManager+Win32.swift
  FileManager+XDG.swift
  Host.swift
  Notification.swift
  NotificationQueue.swift
  NSKeyedArchiver.swift
  NSKeyedArchiverHelpers.swift
  NSKeyedCoderOldStyleArray.swift
  NSKeyedUnarchiver.swift
  NSNotification.swift
  NSSpecialValue.swift
  Port.swift
  PortMessage.swift
  Process.swift
  ProcessInfo.swift
  PropertyListEncoder.swift
  RunLoop.swift
  Stream.swift
  Thread.swift
  Timer.swift
  UserDefaults.swift)
endif()

add_library(Foundation
  AffineTransform.swift
  Array.swift
  Boxing.swift
  Bridging.swift
  Bundle.swift
  ByteCountFormatter.swift
  Calendar.swift
  CGFloat.swift
  CharacterSet.swift
  Codable.swift
  Collections+DataProtocol.swift
  ContiguousBytes.swift
  Data.swift
  DataProtocol.swift
  Date.swift
  DateComponents.swift
  DateComponentsFormatter.swift
  DateFormatter.swift
  DateInterval.swift
  DateIntervalFormatter.swift
  Decimal.swift
  Dictionary.swift
  DispatchData+DataProtocol.swift
  EnergyFormatter.swift
  ExtraStringAPIs.swift
  FileHandle.swift
  FileManager.swift
  Formatter.swift
  FoundationErrors.swift
  IndexPath.swift
  IndexSet.swift
  ISO8601DateFormatter.swift
  JSONDecoder.swift
  JSONEncoder.swift
  JSONSerialization.swift
  JSONSerialization+Parser.swift
  LengthFormatter.swift
  Locale.swift
  MassFormatter.swift
  Measurement.swift
  MeasurementFormatter.swift
  NSArray.swift
  NSAttributedString.swift
  NSCache.swift
  NSCalendar.swift
  NSCFArray.swift
  NSCFBoolean.swift
  NSCFCharacterSet.swift
  NSCFDictionary.swift
  NSCFSet.swift
  NSCFString.swift
  NSCFTypeShims.swift
  NSCharacterSet.swift
  NSCoder.swift
  NSComparisonPredicate.swift
  NSCompoundPredicate.swift
  NSConcreteValue.swift
  NSData+DataProtocol.swift
  NSData.swift
  NSDate.swift
  NSDateComponents.swift
  NSDecimalNumber.swift
  NSDictionary.swift
  NSEnumerator.swift
  NSError.swift
  NSExpression.swift
  NSGeometry.swift
  NSIndexPath.swift
  NSIndexSet.swift
  NSLocale.swift
  NSLock.swift
  NSLog.swift
  NSMeasurement.swift
  NSNull.swift
  NSNumber.swift
  NSObjCRuntime.swift
  NSObject.swift
  NSOrderedSet.swift
  NSPathUtilities.swift
  NSPersonNameComponents.swift
  NSPlatform.swift
  NSPredicate.swift
  NSRange.swift
  NSRegularExpression.swift
  NSSet.swift
  NSSortDescriptor.swift
  NSString.swift
  NSStringAPI.swift
  NSSwiftRuntime.swift
  NSTextCheckingResult.swift
  NSTimeZone.swift
  NSURL.swift
  NSURLComponents.swift
  NSURLQueryItem.swift
  NSURLError.swift
  NSUUID.swift
  NSValue.swift
  NumberFormatter.swift
  Operation.swift
  PersonNameComponents.swift
  PersonNameComponentsFormatter.swift
  Pointers+DataProtocol.swift
  Progress.swift
  ProgressFraction.swift
  PropertyListSerialization.swift
  ReferenceConvertible.swift
  Scanner.swift
  ScannerAPI.swift
  Set.swift
  String.swift
  StringEncodings.swift
  TimeZone.swift
  Unit.swift
  URL.swift
  URLComponents.swift
  URLQueryItem.swift
  URLResourceKey.swift
  UUID.swift
  ${FOUNDATION_WASI_UNAVAILABLE_SOURCES})
target_compile_definitions(Foundation PRIVATE
  DEPLOYMENT_RUNTIME_SWIFT)
target_compile_options(Foundation PUBLIC
  $<$<BOOL:${ENABLE_TESTING}>:-enable-testing>
  "SHELL:-Xcc -F${CMAKE_BINARY_DIR}")
target_link_libraries(Foundation
  PRIVATE
    $<$<PLATFORM_ID:Windows>:CoreFoundationResources>
    $<$<PLATFORM_ID:Windows>:Ole32>
    $<$<PLATFORM_ID:Windows>:Shell32>
    $<$<PLATFORM_ID:Windows>:pathcch>
    CoreFoundation
    uuid
  PUBLIC
    swiftDispatch)
set_target_properties(Foundation PROPERTIES
  INSTALL_RPATH "$ORIGIN"
  BUILD_RPATH "$<TARGET_FILE_DIR:swiftDispatch>"
  Swift_MODULE_DIRECTORY ${CMAKE_BINARY_DIR}/swift
  INTERFACE_INCLUDE_DIRECTORIES ${CMAKE_BINARY_DIR}/swift)

if(NOT BUILD_SHARED_LIBS)
  add_dependencies(Foundation CoreFoundation uuid)
# TODO(katei): Comment out after swift-frontend implementation
# https://github.com/apple/swift/pull/35936
#  target_compile_options(Foundation
#    PRIVATE
#      "SHELL:-public-autolink-library icui18n")
  set_property(TARGET Foundation PROPERTY STATIC_LIBRARY_OPTIONS
    $<TARGET_OBJECTS:CoreFoundation>
    $<TARGET_OBJECTS:uuid>
    $<TARGET_OBJECTS:BlocksRuntime>)
endif()

if(CMAKE_SYSTEM_NAME STREQUAL Windows)
  # NOTE: workaround for CMake which doesn't link in OBJECT libraries properly
  add_dependencies(Foundation CoreFoundationResources)
  target_link_options(Foundation PRIVATE
    $<TARGET_OBJECTS:CoreFoundationResources>)
elseif(NOT CMAKE_SYSTEM_NAME STREQUAL Darwin)
  target_link_options(Foundation PRIVATE "SHELL:-no-toolchain-stdlib-rpath")
endif()


set_property(GLOBAL APPEND PROPERTY Foundation_EXPORTS Foundation)
_install_target(Foundation)
