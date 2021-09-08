# prometheus
ejemplito de prometeus

```java
package gov.afip.pampa.consumer;



import java.net.InetSocketAddress;

import gov.afip.pampa.common.service.Service;
import gov.afip.pampa.services.AbstractService;
import gov.afip.pampa.services.events.EventCoordinator;
import gov.afip.pampa.services.events.EventListener;
import io.prometheus.client.CollectorRegistry;
import io.prometheus.client.Counter;
import io.prometheus.client.Histogram;
import io.prometheus.client.exporter.HTTPServer;
import io.prometheus.client.hotspot.DefaultExports;

/**
 *
 * ConsumerManager.java
 *
 * @author La banda de BID
 * @version $Id: PrometheusManager.java 18868 hoy $
 */
public interface PrometheusManager extends Service {

	public void start();

	public void stop();

	public String getState();

	public boolean isStarted();

	public boolean isStoped();
	/*
	static final Counter counter_clasif_exito = Counter.build().name("bid_clasificacion_exito").help("Contador exitos en clasificacion").register();
	static final Counter counter_clasif_error = Counter.build().name("bid_clasificacion_error").help("Contador errores en clasificacion").register();
	static final Counter counter_reg_exito = Counter.build().name("bid_registracion_exito").help("Contador exitos en registracion").register();
	static final Counter counter_reg_error = Counter.build().name("bid_registracion_error").help("Contador errores en registracion.").register();
	*/
	static final Counter counterMetric = Counter.build().name("bid_counter").help("Contador ...").labelNames("proceso", "tipo").register();
	
	static final Histogram histoTime = Histogram.build().name("bid_histogram")
			.help("Tiempo de registraciones.")
			.buckets(15,30,45,60,120,240,480)
			//.linearBuckets(1, 2, 3)
			.labelNames("mensaje", "proceso", "evento").register();
	
	
	class DefaultImpl extends AbstractService implements PrometheusManager, EventListener {

		private static final long serialVersionUID = -5583017687658760051L;

		private boolean started = false;
		private boolean stoped = true;
		private HTTPServer prometheusServer;
		public void start() {
			doStart();
		}

		public void stop() {
			doStop();
		}

		public String getState() {
			if (started) {
				return "Started";
			} else {
				return "Stoped";
			}
		}

		private void doStart() {
			if (!started) {

				
//				Server server = new Server(1234);
//				ServletContextHandler context = new ServletContextHandler();
//				context.setContextPath("/");
//				server.setHandler(context);
//				context.addServlet(new ServletHolder(new ExampleServlet()), "/");
//				context.addServlet(new ServletHolder(new MetricsServlet()), "/metrics");
//				DefaultExports.initialize();
//				System.out.println("Levantando el server");
//				server.start();
				
				
			     InetSocketAddress socket;
			     int port = 8443;
			     socket = new InetSocketAddress("localhost", port);
			     

			    try {
			    	this.prometheusServer = new HTTPServer(socket, CollectorRegistry.defaultRegistry);
				    DefaultExports.initialize();
				} catch (Exception e) {
					// TODO: handle exception
				}
			    
			    started = true;
				stoped = false;
//				
//				
//				prometheusServer = new Server(1234);
//				ServletContextHandler context = new ServletContextHandler();
//				context.setContextPath("/");
//				prometheusServer.setHandler(context);
//				//context.addServlet(new ServletHolder(new ExampleServlet()), "/");
//				context.addServlet(new ServletHolder(new MetricsServlet()), "/metrics");
//				DefaultExports.initialize();
//				System.out.println("Levantando el server");
//				

//				try {
//					prometheusServer.start();
//					started = true;
//					stoped = false;
//				} catch (Exception e) {
//					// TODO Auto-generated catch block
//					e.printStackTrace();
//				}
			}

		}

		private void doStop() {
			if (!stoped) {
				try {
					this.prometheusServer.stop();
					stoped = true;
					started = false;
				} catch (Exception e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
		}

		public void initializeService() {
		}

		public void handle(String eventId, Object param) {
			if (eventId.equals("dit.sct.common.event.StartupEvent")) {
				start();
			} else if (eventId.equals("dit.sct.common.event.ShutdownEvent")) {
				stop();
			}
		}

		/**
		 * @param coordinator
		 */
		public void setEventCoordinator(EventCoordinator coordinator) {
			coordinator.addEventListener(this);
		}

		public HTTPServer getPrometheusServer() {
			return prometheusServer;
		}

		public void setPrometheusServer(HTTPServer prometheusServer) {
			this.prometheusServer = prometheusServer;
		}

		@Override
		public boolean isStarted() {
			// TODO Auto-generated method stub
			return this.started;
		}

		@Override
		public boolean isStoped() {
			// TODO Auto-generated method stub
			return this.stoped;
		}

	}

}

```
